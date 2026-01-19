# Policy Engine

**Générer automatiquement des politiques de sécurité et du code d’infrastructure  
à partir d’une description d’architecture.**

Les infrastructures évoluent en permanence.  
Les standards de sécurité sont publiés sous forme de documents statiques.

Entre les deux, les organisations s’appuient encore largement sur :
- du travail manuel,
- du copier-coller fragile,
- des interprétations humaines variables,
- et des politiques souvent obsolètes avant même leur validation.

**Policy Engine comble cet écart par de l’ingénierie, pas par des approches opaques.**

---

## 🎯 Objectif du projet

Policy Engine est un moteur d’automatisation de politiques de sécurité.

Son objectif est de transformer une description d’infrastructure réelle en :
- un ensemble de contrôles de sécurité applicables,
- une implémentation technique cohérente,
- une documentation claire et traçable.

Le projet vise à relier explicitement :  
**architecture → contrôles → implémentation**.

---

## 🧭 Exemple conceptuel

### Situation initiale
Une infrastructure contient des composants exposés publiquement, sans chiffrement ni journalisation suffisante.

### Ce que fait le moteur
- Analyse la description de l’infrastructure
- Identifie les composants critiques
- Détecte les écarts de sécurité
- Sélectionne les contrôles applicables
- Génère un socle de sécurité cohérent
- Produit un rapport explicatif

### Résultat attendu
- Accès public supprimé
- Chiffrement activé
- Journalisation configurée
- Alignement explicite avec des standards reconnus

---

## 🔍 Pourquoi ce projet est pertinent

- La valeur est compréhensible en quelques secondes
- Les décisions sont explicables et traçables
- Le résultat est directement exploitable
- Le lien entre conformité et implémentation est explicite
- Le projet démontre un raisonnement d’ingénierie

---

## 🔄 Logique de fonctionnement

### Vue synthétique
Infrastructure décrite  
→ Analyse  
→ Modèle interne normalisé  
→ Sélection des contrôles  
→ Génération du socle de sécurité  
→ Validation  
→ Résultat final

### Vue conceptuelle
          ┌─────────────┐
          │  Terraform  │──┐
          │   ou YAML   │  │
          └─────────────┘  │
                           ▼
                   ┌──────────────┐
                   │   Parser     │
                   └──────────────┘
                           ▼
                   ┌──────────────┐
                   │ Modèle       │  ← Schéma normalisé
                   │ Interne      │     (types, relations)
                   └──────────────┘
                           ▼
                   ┌──────────────┐
                   │  Mapping     │  ← Requête base de 
                   │  Engine      │     contrôles (PostgreSQL)
                   └──────────────┘
                           ▼
                   ┌──────────────┐
                   │ Générateur   │  ← Templates Terraform
                   │ Terraform    │     + validation
                   └──────────────┘
                           ▼
                   ┌──────────────┐
                   │ Validation   │  ← terraform validate
                   │              │     + checkov
                   └──────────────┘
                           ▼
                  ✅ Code + Rapport

---

## 🧠 Principes d’ingénierie

- **Séparation des responsabilités**  
  Comprendre, décider et générer sont des étapes distinctes.

- **Déterminisme**  
  Les décisions reposent sur des règles explicites.

- **Traçabilité**  
  Chaque contrôle est justifié et documenté.

- **Prudence opérationnelle**  
  Aucun changement direct sur l’infrastructure existante.

- **Lisibilité**  
  Les sorties sont compréhensibles par des humains.

---

## 📦 Portée actuelle — Phase 1

La phase actuelle se concentre sur :
- un périmètre fonctionnel volontairement restreint,
- un nombre limité de contrôles,
- une validation technique stricte,
- une démonstration claire de valeur.

L’objectif n’est pas l’exhaustivité, mais la fiabilité.

---

## ⚠️ Limites assumées

- Certaines informations peuvent être incomplètes
- Certains contrôles nécessitent une validation humaine
- La conformité est un processus continu

Ces limites sont connues et assumées.

---

## 🚀 Évolution prévue

- **Phase 2**  
  Interface web, rapports de conformité lisibles, accessibilité non technique.

- **Phase 3**  
  Déploiement contrôlé, validation humaine, journal d’audit complet.

Chaque phase repose sur la solidité de la précédente.

---

## 🎯 Positionnement

Policy Engine n’est pas :
- un scanner passif,
- un outil de conformité marketing,
- un moteur de déploiement aveugle.

C’est un **outil d’ingénierie de sécurité**, conçu pour relier architecture, politiques et implémentation de manière cohérente et vérifiable.

---
