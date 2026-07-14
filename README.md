# Augmented Reality Sehsimulation

Eine WebAR Anwendung, die Sehbehinderungen simuliert, um Nutzer*innen ein Gefühl dafür zu geben, wie Betroffene die Welt 
wahrnehmen. 
Hauptbestandteil ist ein interaktives AR-Lernspiel, bei dem verschiedene Sehbehinderungen aktiv eine Aufgabe erschweren 
und zusätzlich gibt es einen Simulationsmodus, in dem die Sehbehinderungen frei erkundet werden kann.

## Motivation
Weltweit leben mehr als 100 Millionen Menschen mit einer chronischen Sehbehinderung, wobei Glaukom, altersbedingte 
Makuladegeneration und Grauer Star zu den häufigsten Ursachen zählen (Jones et al., 2020).

Sehbehinderungen betreffen häufig nur einen bestimmten Teil des Gesichtsfelds, etwa vorwiegend das periphere Sehen bei 
Glaukom oder vorwiegend das zentrale Sehen bei Makuladegeneration. Dabei fällt die betroffene Region meist nicht komplett aus, 
sondern wird auf unterschiedliche Weise beeinträchtigt, zum Beispiel verschwommen, blass oder verzerrt. Die konkreten 
Symptome unterscheiden sich individuell, zwischen den beiden Augen und über die Zeit hinweg (Jones et al., 2020).

Wegen dieser Häufigkeit und Vielschichtigkeit werden Simulationen in mehreren Bereichen eingesetzt. In der Medizin dienen 
sie der Aufklärung der Öffentlichkeit oder informieren Angehörige über die Bedürfnisse einer betroffenen Person. 
In der Sehforschung werden sie genutzt, um Auswirkungen auf Alltagsaufgaben zu untersuchen. In Gesundheitsökonomie und im 
Produktdesign helfen sie dabei, den Nutzen neuer Behandlungen oder die Zugänglichkeit von Produkten und Umgebungen einzuschätzen 
(Jones et al., 2020). Genau in diesem Feld will dieses Projekt ansetzen, mit dem Ziel Empathie zu fördern.

## Technischer Aufbau

Die Anwendung ist eine einzelne `index.html`, ohne Build Prozess und ohne Framework außer A-Frame und AR.js, beide über 
Content Delivery Network (CDN) eingebunden. Getrackt wird über einen Hiro Marker.

AR.js legt zwei getrennte Ebenen an: ein Video Element mit dem rohen Kamerabild und einen WebGL Canvas von A-Frame für 
alle virtuellen Objekte, mit transparentem Hintergrund. Beide Ebenen werden unabhängig behandelt.

Für die Sehsimulation gibt es zwei Mechanismen. Katarakt (Grauer Star) und Rot Grün Schwäche werden beispielsweise über CSS Filter 
direkt auf Video und Canvas angewendet, das verändert die tatsächlichen Pixel beider Ebenen gleichzeitig. 
Glaukom und Makuladegeneration laufen hingegen über einen Canvas 2D Overlay, der in einer eigenen Render Schleife jeden 
Frame neu gezeichnet wird und automatisch über allem liegt, egal was darunter passiert.

Die Trennung von Video und Canvas fällt in der reinen Simulation nicht auf, weil dort keine virtuellen Objekte im Bild 
sind. Relevant wird sie im Lernspiel, dort sitzen die Luftballons auf dem WebGL Canvas, und ohne den Filter auch dort 
wären sie von der jeweiligen Sehbehinderung unberührt, obwohl sie der entscheidende Teil der Aufgabe sind.

Das Lernspiel wählt pro Runde zufällig eine der vier Sehbehinderungen (oder keine) aus, aktiviert die passende Simulation 
und lässt Luftballons aufsteigen, die angetippt werden müssen. Rote Ballons zählen als Fehler. Die Erkennung eines 
Treffers berechnet für jeden Ballon die Position auf dem Bildschirm aus seiner 3D Position und dem Kamerawinkel und 
sucht den nächsten Treffer in einem festen Radius.

Die Definition von Augmented Reality nach Azuma erfüllt das Lernspiel, da die Luftballons als virtuelle Objekte in 
Echtzeit relativ zueinander und zum Marker bewegt werden, während die Nutzerin oder der Nutzer sie direkt per 
Antippen beeinflusst.

## Technische Einschränkungen

CSS Filter lassen sich nicht auf die a-scene als Ganzes anwenden, sie müssen einzeln auf das Video Element und den 
WebGL Canvas gesetzt werden. Der WebXR dom overlay funktioniert im Oculus Browser nicht zuverlässig, deshalb habe ich 
entschieden, die Anwendung als normale Webseite im Browser laufen zu lassen, statt als immersive WebXR Session.

## Limitationen

Klassische Sehbehinderungssimulatoren bestanden früher meist aus einem statischen Bild oder einer Brille mit einem schwarzen 
Fleck über einem Teil des Gesichtsfelds. Betroffene empfanden solche Darstellungen häufig als grob und unrealistisch, 
unter anderem weil man mit den Augen einfach um den Fleck herumschauen und die Beeinträchtigung so umgehen kann. 
Ein späterer Ansatz mit einer teilweise undurchsichtigen Kontaktlinse bewegt sich zwar mit dem Auge mit, erzeugt aber 
einen diffusen Schatten über das ganze Sehfeld statt der eigentlich lokal begrenzten Beeinträchtigung, die 
Betroffene beschreiben (Jones et al., 2020).

Diese Kritik betrifft auch meine aktuelle Umsetzung von Glaukom und Makuladegeneration in diesem Projekt. Die Vignette 
bzw. der zentrale Fleck sind an feste Bildschirmkoordinaten gebunden, nicht an die tatsächliche Blickrichtung der 
Nutzerin oder des Nutzers. Wer die Augen bewegt statt den Kopf, kann die simulierte Beeinträchtigung im Prinzip umgehen, 
genau das Problem, das in der Literatur an klassischen SimSpecs kritisiert wird. Eine blickabhängige (gaze-contingent) 
Umsetzung würde Eye-Tracking-Hardware voraussetzen, die im Rahmen dieses Projekts nicht zur Verfügung stand.


## Verwendete Bibliotheken

**A-Frame** (Version 1.4.0), Framework für WebVR/WebAR-Szenen auf Basis von Three.js.
https://aframe.io/releases/1.4.0/aframe.min.js

**AR.js** (aframe-ar Build, master Branch), Marker-basiertes Tracking für A-Frame.
https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js

Beide Bibliotheken wurden ausschließlich über CDN eingebunden, es wurde kein Code aus ihnen kopiert oder verändert.

## Verwendete externe Ressourcen

**Hiro-Marker**, Standard-Trackingbild von AR.js, keine eigene Erstellung.
https://github.com/AR-js-org/AR.js/blob/master/data/images/hiro.png

Alle 3D-Objekte (Luftballons, Marker-Inhalte) wurden ausschließlich aus A-Frame Grundprimitiven (Kugel, Kegel, Zylinder) 
zusammengesetzt, es wurden keine externen 3D-Modelle, Texturen oder Sounds verwendet.


## Literatur

Jones, P.R., Somoskeöy, T., Chow-Wing-Bom, H. et al. Seeing other perspectives: evaluating the use of virtual and 
augmented reality to simulate visual impairments (OpenVisSim). 
npj Digit. Med. 3, 32 (2020). https://doi.org/10.1038/s41746-020-0242-6