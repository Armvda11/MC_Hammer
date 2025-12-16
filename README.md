# MC_Hammer

Projet de support pour la présentation "MC_Hammer" (attaque matérielle).

## Sources

- Vidéo (YouTube) : https://www.youtube.com/watch?v=B6dC_KVLn6Q
- Slides (PDF) : https://media.defcon.org/DEF%20CON%2033/DEF%20CON%2033%20presentations/Christopher%20Domas%20-%20Ghosts%20in%20the%20Machine%20Check%20-%20Conjuring%20Hardware%20Failures%20for%20Cross-ring%20Privilege%20Escalation.pdf

## Résumé des slides

- Contexte et cible : présentation d'une attaque nommée "MCHammer" visant le SMM (System Management Mode, Ring -2) pour obtenir un contrôle matériel absolu.
- Vulnérabilité clé : lors de l'entrée en SMM, le processeur peut encore référencer l'IDT de l'OS (IDTR non mis à jour), permettant l'exécution d'un handler contrôlé par l'attaquant.
- Mécanique d'attaque : fuzzing des registres du Northbridge pour « empoisonner » la configuration et transformer des erreurs bénignes (p.ex. master abort) en Machine Check Exception (MCE) fatales.
- Problème de timing : une MCE standard est très rapide (~700 cycles) alors que l'ouverture de la fenêtre SMM demande beaucoup plus de cycles (~10k+), rendant l'exploitation difficile sans synchronisation.
- Technique de temporisation : saturation du bus PCIe (transactions non alignées) pour fragmenter/ralentir l'opération et étendre la fenêtre d'impact (~12 000 cycles), permettant une MCE retardée au moment où le SMM utilise l'IDT compromis.
- Chaîne d'exploitation : préparer la fausse IDT (piège), lancer l'attaque synchronisée (saturation PCIe), provoquer la MCE retardée et ainsi exécuter du code en SMM (élévation cross-ring).
- Impact post-exploitation : contrôle total et persistant (Ring -2) — invisibilité pour l'OS, persistance via SPI/firmware, exfiltration de clés/mémoire, backdoors matérielles.
- Protections : correctifs microcode/firmware (patch BIOS/UEFI), verrouillage de l'IDT avant gestion MCE, meilleures isolations matérielles; architectures récentes mitigent partiellement le vecteur.

