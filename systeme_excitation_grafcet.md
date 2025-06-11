```mermaid
graph TD
    %% Définition des classes pour un meilleur rendu visuel
    classDef initial fill:#f9f,stroke:#333,stroke-width:2px,color:#333; %% Etape initiale
    classDef normal fill:#9cf,stroke:#333,stroke-width:2px,color:#333; %% Etape normale
    classDef action fill:#ADD8E6,stroke:#333,stroke-width:1px,color:#333; %% Action (couleur bleu clair)
    classDef transition_bar fill:#333,stroke:#333,stroke-width:1px,height:5px; %% Barre de transition (plus visible)


    %% Etape initiale (double cadre)
    s0((0)):::initial --> t_init;

    %% Transition d'initialisation
    t_init[Initialisation Système / Mise sous tension AVR]:::transition_bar;

    %% Etape 1: Régulateur Automatique Actif
    t_init --> s1;
    s1[1. Régulateur Automatique Actif]:::normal;
    s1 -- "Activer LED VOIE 1" --> A1_1;
    A1_1[Action: Activer LED 'VOIE 1' & 'ACTIV']:::action;
    s1 -- "Assurer tension constante" --> A1_2;
    A1_2[Action: L'alternateur maintient tension constante]:::action;

    %% Transitions depuis l'étape 1
    s1 --> t1_auto_defaut{Défaut Voie Auto (Watchdog OR Mesure Iexc OR Module impulsion thyristor)};
    s1 --> t1_manu_cmd{Appui Opérateur 'DESEL.VOIE'};

    %% Convergence vers l'étape 2
    t1_auto_defaut --> s2;
    t1_manu_cmd --> s2;

    %% Etape 2: Régulateur Manuel Actif
    s2[2. Régulateur Manuel Actif]:::normal;
    s2 -- "Activer LED VOIE 2" --> A2_1;
    A2_1[Action: Activer LED 'VOIE 2' & 'ACTIV']:::action;
    s2 -- "Copie Auto vers Manuel" --> A2_2;
    A2_2[Action: Régulateur Manuel en mode poursuite (recopie de la voie Auto)]:::action;
    s2 -- "Assurer courant d'excitation constant" --> A2_3;
    A2_3[Action: L'alternateur maintient courant d'excitation constant]:::action;


    %% Transitions depuis l'étape 2
    s2 --> t2_manu_defaut{Défaut Voie Manuelle (Ex: Défaut capteur Iexc en mode manuel)};
    s2 --> t2_auto_cmd{Appui Opérateur 'DESEL.VOIE'};


    %% Convergence vers l'étape 1
    t2_manu_defaut --> s1;
    t2_auto_cmd --> s1;

    %% Actions de recopie lors du retour à l'Automatique (souvent des actions associées à la transition plutôt qu'à l'étape elle-même dans les GRAFCET complexes, mais pour la clarté ici on les lie à l'étape cible)
    t2_manu_defaut -- "Copie Manuel vers Auto" --> A1_3[Action: Régulateur Auto en mode poursuite (recopie de la voie Manuel)]:::action;
    t2_auto_cmd -- "Copie Manuel vers Auto" --> A1_3;

    %% Liaison pour que les actions soient bien rattachées visuellement aux transitions/étapes
    A1_1 --o s1;
    A1_2 --o s1;
    A1_3 --o s1;

    A2_1 --o s2;
    A2_2 --o s2;
    A2_3 --o s2;
