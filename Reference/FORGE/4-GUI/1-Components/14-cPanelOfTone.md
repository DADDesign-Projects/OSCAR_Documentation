---
title: cPanelOfTone
parent: GUI Components
layout: default
nav_order: 15
---
# DadGUI::cPanelOfTone

**Namespace :** DadGUI::cPanelOfTone  
**Files :**  cPanelOfTone.h / cPanelOfTone.cpp  
**Directory :** DAD_FORGE/GUI/Components/  
**Inheritance**: `cPanelOfParameterView`, `iGUI_EventListener`  
**Description :** Panel for managing tone output audio (bass, mid, treble).

---

## 📋 Description de la Classe
La classe `cPanelOfTone` est un composant d'interface utilisateur dédié au contrôle de l'égalisation tonale. Elle gère trois bandes de fréquences : Basses (Low-shelf), Médiums (Peaking EQ) et Aigus (High-shelf). La classe intègre des filtres BiQuad pour le traitement du signal audio et permet de lier les paramètres de l'interface graphique au traitement DSP en temps réel.

---

## 🔗 Dépendances Externes

### **Inclusions de Fichiers**

| Fichier Inclus | Provenance | Rôle |
|:---|:---|:---|
| `"GUI_Include.h"` | `DAD_FORGE/GUI` | Contient les définitions de base de l'interface graphique |
| `"cPanelOfParameters.h"` | `DAD_FORGE/GUI` | Définit la classe de base et les types de paramètres |
| `"BiquadFilter.h"` | `DAD_FORGE/DSP` | Définit les classes de filtrage BiQuad |

### **Variables Globales**

| Variable / Objet | Type | Provenance | Rôle |
|:---|:---|:---|:---|
| `SAMPLING_RATE` | `uint32_t` | `extern` | Fréquence d'échantillonnage utilisée pour l'initialisation des filtres |
| `__GUI_EventManager` | `cGUI_EventManager*` | `extern` | Gestionnaire d'événements pour l'abonnement aux événements de traitement audio |

### **Fonctions Externes Appelées**

| Fonction | Provenance | Usage |
|:---|:---|:---|
| `void Subscribe_RT_ProcessOut(iGUI_EventListener*)` | `__GUI_EventManager` | Permet à la classe de recevoir les événements de traitement audio en temps réel |

---

## 📚 Méthodes Publiques

### **~cPanelOfTone**

| Élément | Détails |
|:---|:---|
| **Méthode** | `~cPanelOfTone()` |
| **Description** | Destructeur virtuel de la classe. |

### **Initialize**

| Élément | Détails |
|:---|:---|
| **Méthode** | `void Initialize(uint32_t SerializeID)` |
| **Description** | Initialise les filtres BiQuad (Bass, Mid, Treble) avec leurs fréquences de coupure et types de filtre respectifs, configure les paramètres de contrôle, initialise les vues graphiques et s'abonne aux événements de traitement audio. |
| **Paramètre(s)** | |
| `SerializeID` | `uint32_t` Identifiant de sérialisation pour la persistance des paramètres. |

### **Update**

| Élément | Détails |
|:---|:---|
| **Méthode** | `void Update()` |
| **Description** | Met à jour l'état du panneau si celui-ci est actif, en appelant la logique de mise à jour de la classe parente. |

### **Process**

| Élément | Détails |
|:---|:---|
| **Méthode** | `void Process(AudioBuffer *pIn, AudioBuffer *pOut)` |
| **Description** | Traite le flux audio en appliquant successivement les filtres de basse, de médium et d'aigu sur le buffer. |
| **Paramètre(s)** | |
| `pIn` | `AudioBuffer*` Buffer d'entrée audio. |
| `pOut` | `AudioBuffer*` Buffer de sortie audio. |

### **BassChange**

| Élément | Détails |
|:---|:---|
| **Méthode** | `static void BassChange(DadDSP::cParameter* pParameter, uint32_t Data)` |
| **Description** | Callback statique pour le changement du paramètre de basse. Met à jour le gain du filtre BiQuad de basse. |
| **Paramètre(s)** | |
| `pParameter` | `DadDSP::cParameter*` Le paramètre de basse modifié. |
| `Data` | `uint32_t` Contexte (pointeur vers l'instance de la classe). |

### **MidChange**

| Élément | Détails |
|:---|:---|
| **Méthode** | `static void MidChange(DadDSP::cParameter* pParameter, uint32_t Data)` |
| **Description** | Callback statique pour le changement du paramètre de médium. Met à jour le gain du filtre BiQuad de médium. |
| **Paramètre(s)** | |
| `pParameter` | `DadDSP::cParameter*` Le paramètre de médium modifié. |
| `Data` | `uint32_t` Contexte (pointeur vers l'instance de la classe). |

### **TrebleChange**

| Éllement | Détails |
|:---|:---|
| **Méthode** | `static void TrebleChange(DadDSP::cParameter* pParameter, uint32_t Data)` |
| **Description** | Callback statique pour le changement du paramètre d'aigu. Met à jour le gain du filtre BiQuad d'aigu. |
| **Paramètre(s)** | |
| `pParameter` | `DadDSP::cParameter*` Le paramètre d'aigu modifié. |
| `Data` | `uint32_t` Contexte (pointeur vers l'instance de la classe). |


---

## 📦 Données

### Variables Protégées / Privées

| Membre | Type | Description |
|:---|:---|:---|
| `dumm[40]` | `char[40]` | Variable de remplissage (padding). |
| `m_BassBiQuad` | `DadDSP::cBiQuad` | Filtre BiQuad pour le contrôle des basses. |
| `m_MidBiQuad` | `DadDSP::cBiQuad` | Filtre BiQuad pour le contrôle des médiums. |
| `m_TrebleBiQuad` | `DadDSP::cBiQuad` | Filtre BiQuad pour le contrôle des aigus. |
| `m_Bass` | `DadGUI::cUIParameter` | Paramètre de niveau pour les basses. |
| `m_Mid` | `DadGUI::cUIParameter` | Paramètre de niveau pour les médiums. |
| `m_Treble` | `DadGUI::cUIParameter` | Paramètre de niveau pour les aigus. |
| `m_BassView` | `DadGUI::cParameterNumLeftRightView` | Vue graphique pour le paramètre Bass. |
| `m_MidView` | `DadGUI::cParameterNumLeftRightView` | Vue graphique pour le paramètre Mid. |
| `m_TrebleView` | `DadGUI::cParameterNumLeftRightView` | Vue graphique pour le paramètre Treble. |

<BR>

---
**DAD_FORGE/DSP** - Bibliothèque DSP Dad Design  
Copyright (c) 2024-2026 Dad Design.
