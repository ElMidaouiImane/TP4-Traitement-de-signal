<div align="center">
  <h1 align="center">TP4 – Filtrage Analogique</h1>
</div>

<summary>Table of Contents</summary>
  <ol>              
      <li><a href="#Objectifs">Objectifs</a></li>
      <li><a href="#Filtrage et diagramme de Bode">Filtrage et diagramme de Bode</a></li> 
      <li><a href="#Dé-bruitage d'un signal sonore">Dé-bruitage d'un signal sonore</a></li> 
  </ol>
  
   # Objectifs

> • Appliquer un filtre réel pour supprimer les composantes indésirables d’un signal.   
> • Améliorer la qualité de filtrage en augmentant l’ordre du filtre.
>
> **Commentaires** : Il est à remarquer que ce TP traite en principe des signaux continus.
> Or, l'utilisation de Matlab suppose l'échantillonnage du signal. Il faudra donc être
> vigilant par rapport aux différences de traitement entre le temps continu et le temps
> discret.
> 
> **Tracé des figures** : toutes les figures devront être tracées avec les axes et les
> légendes des axes appropriés.
> 
> **Travail demandé** : un script Matlab commenté contenant le travail réalisé et des
> commentaires sur ce que vous avez compris et pas compris, ou sur ce qui vous a
> semblé intéressant ou pas, bref tout commentaire pertinent sur le TP.  

# Filtrage et diagramme de Bode  
Nous souhaitons appliquer un filtre passe-haut pour supprimer la composante à 50 Hz. Soit notre signal d'entrée :<br/><br/>
&nbsp;&nbsp;&nbsp;&nbsp;**x(t) = sin(2.pi.f1.t) + sin(2.pi.f2.t) + sin(2.pi.f3.t)**  <br/><br/>
Avec f1 = 500 Hz, f2 = 400 Hz et f3 = 50 Hz  
<br/>
1. Définir le signal x(t) sur t = [0 5] avec Te = 0,0001 s.
```matlab
%Definition des variables et de signal 
te = 1e-4 ;
fe = 1/te ;
t = 0:te:5 ;
x = sin(2*pi*500*t)+ sin(2*pi*400*t)+ sin(2*pi* 50*t) ;
N = length(x);
```
2. Tracer le signal x(t) et sa transformé de Fourrier. Qu'observez-vous ?
```matlab
plot(t,x)
title("le signal x")
```
![signal x](https://user-images.githubusercontent.com/121026639/215267359-d25227f0-0715-4dae-a246-91248a16b681.png)

```matlab
f = (0:N-1)*(fe/N);
fshift = (-N/2:(N/2)-1)*fe/N;
y = fft(x);
plot(fshift, fftshift(abs(y)/N)*2)
title('Le spectre Amplitude')
```

![spectre x](https://user-images.githubusercontent.com/121026639/215267433-716e56b3-74ef-4ba5-aa11-97daeed6b951.png)

(Essayez de tracer avec Te = 0,0005 s. Remarques ?)

![0 0005](https://user-images.githubusercontent.com/121026639/215267484-1116857e-3cc3-4c35-ba4c-f41d8ca84b7c.png)
![0 0005 spectre](https://user-images.githubusercontent.com/121026639/215267490-d11d3268-9954-46ee-8498-0e1309986a31.png)

##### L'amplitude a diminué  

La fonction H(f) (transmittance complexe) du filtre passe haut de premier ordre est donnée par :<br/><br/>
&nbsp;&nbsp;&nbsp;&nbsp;**H(f) = (K.j.w/wc) / (1 + j. w/wc)**  <br/><br/>
Avec K le gain du signal, w la pulsation et wc la pulsation de coupure.  
On se propose de tracer le diagramme de Bode de ce filtre et de l'appliquer au signal.  

1. Tracer le module de la fonction H(f) avec K=1 et wc = 50 rad/s.

```matlab
K = 1 ;
wc1 = 50 ;
w = 2*pi*f ; 
H1 = (K*1j*w/wc1)./(1+1j*w/wc1) ;
plot(f,abs(H1))
title('La fonction H(f)')
```
![H(f)](https://user-images.githubusercontent.com/121026639/215267731-cc0a7b27-ee58-4448-8b59-16664975739f.png)

2. Tracer 20.log(|H(f)|) pour différentes pulsations de coupure wc, qu'observez-vous ? (Afficher avec semilogx)
#### on essaye avec 50Hz , 400Hz et 700Hz
```matlab
K = 1 ;
wc1 = 50 ;
wc2 = 400 ;
wc3 = 700 ;
w = 2*pi*f ; 

H1 = (K*1j*w/wc1)./(1+1j*w/wc1) ;
H2 = (K*1j*w/wc2)./(1+1j*w/wc2) ;
H3 = (K*1j*w/wc3)./(1+1j*w/wc3) ;
G1 = 20*log(abs(H1));
G2 = 20*log(abs(H2));
G3 = 20*log(abs(H3));

phi1 = angle(H1);
phi2 = angle(H2);
phi3 = angle(H3);



subplot(2,1,1) 
semilogx(f,G1,'g',f,G2,'r',f,G3,'b')
ylabel('Gain (dB)')
xlabel('Frequency (rad/s)')
title('Bode Diagram')
grid on
subplot(2,1,2) 
semilogx(f,phi1, 'g',f,phi2,'r',f,phi3,'b')
ylabel('Phase (deg)')
xlabel('Frequency (rad/s)')
grid on
```
![bode diagram](https://user-images.githubusercontent.com/121026639/215267976-09b03107-6a9d-4328-9d8b-da310d368aba.png)

#### Plus le fréquence est petit plus les informations passent 

3. Choisissez différentes fréquences de coupure et appliquez ce filtrage dans l'espace des fréquences. Qu'observez-vous ?
#### #### on essaye avec 50Hz , 400Hz et 700Hz
```matlab
H_filtre1 = [H1(1:floor(N/2)),flip(H1(1:floor(N/2)))];
H_filtre2 = [H2(1:floor(N/2)),flip(H2(1:floor(N/2)))];
H_filtre3 = [H3(1:floor(N/2)),flip(H3(1:floor(N/2)))];

yt1 = y(1:end-1).*H_filtre1 ;
yt2 = y(1:end-1).*H_filtre2 ;
yt3 = y(1:end-1).*H_filtre3 ;

plot(fshift, fftshift(abs(yt1)))
title('Spectre filtré 50')

plot(fshift, fftshift(abs(yt2)))
title('Spectre filtré 400')

plot(fshift, fftshift(abs(yt3)))
title('Spectre filtré 700')
```
#### 50Hz
![50](https://user-images.githubusercontent.com/121026639/215268324-4cc77ae5-d71a-4ecd-a16b-3575f73bf017.png)
#### 400Hz
![400](https://user-images.githubusercontent.com/121026639/215268333-f67d08d2-ca1e-4807-857a-881ec1d277f0.png)
#### 700 Hz
![700](https://user-images.githubusercontent.com/121026639/215268338-d26b22e6-3033-4431-927b-7906570ce871.png)

#### Plus le fréquence est grand plus l'amplitude de 50 Hz est diminué

4. Choisissez wc qui vous semble optimal. Le filtre est-il bien choisi ? Pourquoi ?
#### On choisit 2000 Hz

```matlab
wco = 2000 ;
Ho = (K*1j*w/wco)./(1+1j*w/wco) ;
H_filtreo = [Ho(1:floor(N/2)),flip(Ho(1:floor(N/2)))];
yto = y(1:end-1).*H_filtreo ;
plot(fshift, fftshift(abs(yto)))
title('Spectre filtré 2000')
```

![2000](https://user-images.githubusercontent.com/121026639/215268616-c74a6021-181a-41bc-891d-a5cc3fec55ab.png)

#### Si on choisit un plus grand wc on va supprimer le signal informatique

5. Observez le signal y(t) obtenu, puis Comparer-le avec le signal que vous auriez souhaité obtenir. Conclusions ?
```matlab
YTo = ifft(yto,"symmetric");
subplot(2,1,1) 
plot(t(1:end-1),YTo)
title("Le signal obtenu")
y_t = sin(2*pi*500*t)+ sin(2*pi*400*t);
subplot(2,1,2)
plot(t,y_t)
title("le signal ideal")
```

![comparaison](https://user-images.githubusercontent.com/121026639/215269025-083b2b1b-cd7a-445b-bc5c-f29c32c8ece3.png)

#### On peut observer qu'on a supprimé du signal informatique

# Dé-bruitage d'un signal sonore

Dans son petit studio du CROUS, un mauvais futur ingénieur a enregistré une musique en « .wav » avec un très vieux micro. Le résultat est peu concluant, un bruit strident s'est ajouté à sa musique. Heureusement son voisin, expert en traitement du signal est là pour le secourir :<br/><br/>
&nbsp;&nbsp;&nbsp;&nbsp;**« C'est un bruit très haute fréquence, il suffit de le supprimer. » dit-il sûr de lui.**<br/><br/>

1. Proposer une méthode pour supprimer ce bruit sur le signal.
#### On peut utiliser un filtre passe bas pour supprimer le bruit
```matlab
[music, fs] = audioread('test.wav');
music = music';

N = length(music);

f = (0:N-1)*(fs/N);
fshift = (-N/2:N/2-1)*(fs/N);

spectre_music = fft(music);
plot(fshift,fftshift(abs(spectre_music)));
title('spectre audio')
%%

k = 1;
fc = 1000;
%la transmitance complexe 
h = k./(1+1j*(f/fc));

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];

y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");

plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))))
title('spectre filtré avec k = 1')
```
![spectre audio](https://user-images.githubusercontent.com/121026639/215271484-dbc59038-02fb-4369-b766-5fb84cf3f870.png)

![spectre filtré](https://user-images.githubusercontent.com/121026639/215271703-2d01e5a2-bb66-446f-be3f-baa5d557c012.png)


2. Mettez-la en oeuvre. Quelle influence à le paramètre K du filtre que vous avez utilisé ?

#### pour K = 1
```matlab
k = 1;
fc = 1000;
%la transmitance complexe 
h = k./(1+1j*(f/fc));

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];

y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");

%semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)

plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))))
title('spectre filtré avec K = 1')
```
![k=1](https://user-images.githubusercontent.com/121026639/215271781-0cc52bbd-e158-42cd-a151-6489718c6687.png)

#### pour K = 10
```matlab
k = 10;
fc = 1000;
%la transmitance complexe 
h = k./(1+1j*(f/fc));

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];

y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");

%semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)

plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))))
title('spectre filtré avec K = 10')
```
![k=10](https://user-images.githubusercontent.com/121026639/215271912-c74de8c0-715e-4b10-9f28-fb3065de1893.png)

#### On peut observer la différence lorsqu'on écoute les deux versions de signal , avec K = 1 la musique est perdu avec le bruit , et avec K = 10 on fait une amplifiaction de la fréquence et on peut écouter mieux la musique  

3. Quelles remarques pouvez-vous faire notamment sur la sonorité du signal final.




