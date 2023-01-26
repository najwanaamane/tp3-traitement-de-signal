# tp3-traitement-de-signal
**Introduction**

Dans ce rapport, nous allons présenter les différentes étapes de traitement d'un signal ECG (Electrocardiogramme) à l'aide de Matlab. Le but de ce TP est de supprimer le bruit autour du signal produit par un électrocardiographe, et de rechercher la fréquence cardiaque. La méthode qui sera utilisée est l'analyse de la transformée de Fourier du signal ECG échantillonné.
Dans les sections suivantes, nous allons décrire les différentes étapes utilisées pour supprimer le bruit, et obtenir la fréquence cardiaque.

**Suppression du bruit provoqué par les mouvements du corps**

Le complexe QRS est un signal électrique qui reflète la contraction des ventricules et le remplissage des oreillettes. L'onde T représente l'étape de relâchement des ventricules. La fréquence cardiaque peut être déterminée en comptant le nombre de cycles de ces complexes. 
Les signaux ECG peuvent être affectés par différentes sources de bruit qui peuvent rendre difficile l'analyse du signal, il est donc important de supprimer efficacement ces bruits pour obtenir un diagnostic fiable.
1-	Sauvegarder le signal ECG sur votre répertoire de travail, puis charger-le dans Matlab à l’aide la commande load. 

<pre>
load("ecg.mat");
x=ecg;
</pre>

2-	 Ce signal a été échantillonné avec une fréquence de 500Hz. Tracer-le en fonction du temps, puis faire un zoom sur une période du signal.

<pre>

fs=500;
N=length(x)
ts=1/fs
%%tracer ECG en fonction de temps
t=(0:N-1)*ts;
subplot(3,3,1)
plot(t,x);
title("le signal ECG");

</pre>

![2](https://user-images.githubusercontent.com/86806375/214948912-1deff36d-9721-4fd3-9bf1-9d2bd3dd7131.JPG)


3-	  Pour supprimer les bruits à très basse fréquence dues aux mouvements du corps, on utilisera un filtre idéal passe-haut. Pour ce faire, calculer tout d’abord la TFD du signal ECG, régler les fréquences inférieures à 0.5Hz à zéro, puis effectuer une TFDI pour restituer le signal filtré.

<pre>

h = ones(size(x));
fh = 0.5;
index_h = ceil(fh*N/fs);
h(1:index_h)=0;
h(N-index_h+1:N)=0;

filtre=h.*y;
filtre_temp=ifft(filtre,"symmetric");
subplot(3,3,3)
plot(t,x-filtre_temp);
title("signal filtré")


 subplot(3,3,4)
 plot(f,fftshift(abs(fft(filtre_temp))))
 title("spectre restitué")

</pre>

4-	  Tracer le nouveau signal ecg1, et noter les différences par rapport au signal d’origine.
<pre>

ecg1_freq = h.*y;
ecg1 =ifft(ecg1_freq,"symmetric");

plot(t,ecg1);
title("ecg1")
</pre>
![4l](https://user-images.githubusercontent.com/86806375/214948967-a419463a-8932-427c-a36b-cb8399de8afe.JPG)
![42](https://user-images.githubusercontent.com/86806375/214949208-75fe8e32-f55e-417a-a563-80abd8f1d399.JPG)

**Suppression des interférences des lignes électriques**

50Hz Souvent, l'ECG est contaminé par un bruit du secteur 50 Hz qui doit être supprimé. 
5. Appliquer un filtre Notch idéal pour supprimer cette composante. Les filtres Notch sont utilisés pour rejeter une seule fréquence d'une bande de fréquence donnée. 
<pre>

Notch = ones(size(x));
fcn = 50;
index_hcn = ceil(fcn*N/fs)+1;
Notch(index_hcn)=0;
Notch(index_hcn+2)=0;
ecg2_freq = Notch.*fft(ecg);
ecg2 =ifft(ecg2_freq,"symmetric");
subplot(3,3,5)
plot(t,ecg);
xlim([0.5 1.5])
title("signal filtré 50hz")
</pre>

6. Visualiser le signal ecg2 après filtrage
<pre>

plot(t,ecg2);
xlim([0.5 1.5])
</pre>
![6](https://user-images.githubusercontent.com/86806375/214949087-0f051162-f1ea-4ebd-9a5a-43bc6d7ab2a9.JPG)

**Amélioration du rapport signal sur bruit** 
7. Chercher un compromis sur la fréquence de coupure, qui permettra de préserver la forme du signal ECG et réduire au maximum le bruit. Tester différents choix, puis tracer et commenter les résultats.
<pre>
pass_bas = zeros(size(x));
fcb = 30;
index_hcb = ceil(fcb*N/fs);
pass_bas(1:index_hcb)=1;
pass_bas(N-index_hcb+1:N)=1;

ecg3_freq = pass_bas.*fft(ecg2);
ecg3 =ifft(ecg3_freq,"symmetric");
subplot(3,3,7)
plot(t,ecg,"linewidth",1.5);
xlim([0.5 1.5])
subplot(3,3,8)
plot(t,ecg-ecg3);
xlim([0.5 1.5])
</pre>
![7](https://user-images.githubusercontent.com/86806375/214949121-0147f867-cca8-4cea-b3ee-07462b49e5b8.JPG)

 8. Visualiser une période du nouveau signal filtré ecg3 et identifier autant d'ondes que possible dans ce signal (Voir la partie introduction).
<pre>

plot(t,ecg-ecg3);
xlim([0.5 1.5])
</pre>
Identification de la fréquence cardiaque avec la fonction d’autocorrélation 
9. Ecrire un programme permettant de calculer l’autocorrélation du signal ECG, puis de chercher cette fréquence cardiaque de façon automatique. Utiliser ce programme sur le signal traité ecg3 ou ecg2 et sur le signal ECG non traité. NB : il faut limiter l’intervalle de recherche à la plage possible de la fréquence cardiaque. 
<pre>
[c,lags] = xcorr(ecg3,ecg3);
stem(lags/fs,c)
</pre>

10. Votre programme trouve-t-il le bon pouls ? Commenter.

![10](https://user-images.githubusercontent.com/86806375/214949171-bc5ce28b-7b9e-4dab-9d17-1d5d1561426e.JPG)

**Conclusion**

Ce rapport a décrit les différentes étapes de traitement d'un signal ECG utilisant Matlab, avec pour objectif de supprimer le bruit et de calculer la fréquence cardiaque. La méthode choisie était l'analyse de la transformée de Fourier, et les étapes décrites ont montré comment supprimer efficacement le bruit. Enfin, la méthode de calcul de la fréquence cardiaque par autocorrélation a montré qu'il était possible de calculer précisément la fréquence cardiaque à partir d'un signal ECG filtré.

