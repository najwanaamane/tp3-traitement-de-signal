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

// …

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

