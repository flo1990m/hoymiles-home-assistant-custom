# Hoymiles S-Miles Home — Home Assistant (custom)

**Contrôle de la limitation de puissance Hoymiles en watts (100 à 2 000 W), température du micro-onduleur et suivi de production solaire dans Home Assistant.**

Intégration personnalisée Home Assistant utilisant le cloud **Hoymiles S-Miles Home**. Basée sur le projet [Phil-0805/hoymiles-home-assistant](https://github.com/Phil-0805/hoymiles-home-assistant), avec des fonctionnalités supplémentaires de **lecture/écriture de limite de puissance** et de **température de micro-onduleur**.

> **Testé réellement sur une installation Hoymiles HMS-2000-4WB, 4 panneaux photovoltaïques, station S-Miles Home.** La compatibilité des commandes avec d'autres modèles n'est pas garantie. Il s'agit d'une API cloud non officielle susceptible de changer.

## Fonctionnalités

- **Limitation de puissance réglable depuis Home Assistant :** entité `number`, consigne en **watts** de **100 à 2 000 W** (sur l'installation testée).
- **Lecture de la consigne réelle** : si le réglage est modifié dans l'application S-Miles Home, Home Assistant le récupère au prochain rafraîchissement (environ **2 minutes**).
- **Écriture de la consigne vérifiée** : la commande attend la confirmation du cloud, puis relit la valeur enregistrée pour vérifier le résultat. Une commande identique à la consigne connue n'est pas renvoyée.
- **Lecture au démarrage** : l'intégration interroge la consigne initiale avant de créer les entités. L'entité peut rester temporairement indisponible en cas de problème réseau ou cloud.
- **Température interne du micro-onduleur** à partir du quota `MI_TEMPERATURE` de l'API Protobuf Hoymiles.
- **Production photovoltaïque en temps réel** et informations de la station conservées depuis l'intégration d'origine.
- **Puissance, tension et courant par entrée PV** (PV1 à PV4) depuis les données de graphique Hoymiles ; actualisation demandée toutes les 5 minutes, les valeurs fournies par le cloud pouvant avoir du retard.
- Les autres fonctionnalités existantes de l'intégration d'origine sont préservées, notamment ses capteurs et fonctions de batterie lorsque les équipements et les données correspondants sont disponibles.

### Exemple d'entités sur l'installation testée

| Entité | Utilité |
| --- | --- |
| `number.s_miles_home_15526120_limitation_de_puissance` | Lire et modifier la limite de sortie AC en W |
| `sensor.hoymiles_microinverter_temperature` | Température interne du micro-onduleur |
| `sensor.s_miles_home_15526120_pv_power` | Puissance solaire instantanée |
| `sensor.hoymiles_microinverter_pv1_power` à `pv4_power` | Puissances DC par entrée PV |

Ces identifiants sont des **exemples propres à l'installation de test** : ils dépendent de votre installation et des noms générés par Home Assistant.

## Installation dans HACS (dépôt personnalisé)

Ce dépôt **n'est pas soumis au catalogue officiel HACS**. Il peut être ajouté manuellement :

1. Dans Home Assistant, ouvrir **HACS**.
2. Aller dans le menu des **dépôts personnalisés**.
3. Ajouter l'adresse `https://github.com/flo1990m/hoymiles-home-assistant-custom` avec la catégorie **Intégration**.
4. Installer **Hoymiles S-Miles Home** depuis ce dépôt, puis redémarrer Home Assistant.
5. Ajouter/configurer l'intégration **Hoymiles S-Miles Home** dans **Paramètres → Appareils et services** à l'aide du compte S-Miles Home.

**Important :** si vous utilisez déjà une intégration utilisant le domaine `hoymiles_home`, ne conservez pas deux copies du même domaine. Vérifiez votre installation avant de changer de dépôt. Effectuez une sauvegarde avant toute mise à jour.

## Fonctionnement du contrôle de puissance

La commande est celle validée avec le cloud S-Miles Home pour la station testée :

- **Lecture :** `POST /pvmc/api/0/station_ctl/read_c` avec `action: 84`, puis lecture du résultat avec `setting_status_c`.
- **Écriture :** `POST /pvmc/api/0/station_ctl/write_c` avec `action: 84`, l'identifiant de station (`sid`) et la consigne `power` en **watts**.
- **Vérification :** attente du résultat de commande et relecture par l'API après écriture.

Le contrôle a été **testé de bout en bout** : un changement de **1 425 à 1 500 W** a été confirmé à la fois par l'API et par l'application S-Miles Home, puis la commande depuis Home Assistant à **1 000 W** a également fonctionné et s'est reflétée dans l'application.

La limitation réduit aussi la puissance DC réellement extraite des panneaux : les capteurs PV1 à PV4 **n'indiquent pas la puissance maximale potentiellement disponible** lorsque l'onduleur est bridé.

**Précautions :** éviter de modifier la limite de façon répétée à quelques secondes d'intervalle. Le nombre d'écritures tolérées par le matériel et le cloud n'a pas été caractérisé. Une régulation solaire automatique sur mesureur réseau n'est **pas incluse** dans cette version.

## Versions personnalisées

- **`0.3.0-custom.8`** : lecture initiale de la limitation au démarrage pour éviter une valeur indisponible jusqu'au prochain rafraîchissement.
- **`0.3.0-custom.7`** : ajout de la lecture et de l'écriture de la limitation de puissance en watts, contrôlées par l'action cloud 84, avec confirmation, relecture et entité `number`.
- **`0.3.0-custom.6`** : remontée de la température de micro-onduleur depuis le quota Protobuf `MI_TEMPERATURE`, en conservant les capteurs existants et les corrections de batterie.

## Mises à jour et projet d'origine

Ce dépôt est une **variante indépendante** du projet de Phil, et non une mise à jour automatique de son dépôt. Les évolutions du projet d'origine ne sont pas fusionnées automatiquement ici ; elles doivent être intégrées et vérifiées pour préserver les modifications propres à cette variante.

**Mots-clés :** Home Assistant, HACS custom repository, Hoymiles, S-Miles Home, HMS-2000-4WB, inverter power limit, power regulation, limitation de puissance, solar PV, microinverter temperature, temperature onduleur, photovoltaïque, cloud API.
