---
title: Débuter avec le Security Monitoring
kind: documentation
description: Découvrez les grands concepts de la solution Security Monitoring de Datadog, activez la détection des menaces et explorez les règles de détection des menaces par défaut.
aliases:
  - /fr/security_monitoring/getting_started/
  - /fr/security_platform/getting_started/
further_reading:
  - link: /security_platform/default_rules
    tag: Documentation
    text: Explorer les règles de sécurité par défaut
  - link: /security_platform/explorer
    tag: Documentation
    text: En savoir plus sur le Security Signals Explorer
---
Pour commencer à utiliser la solution Security Monitoring de Datadog, suivez ces étapes :

* [Ingérer des logs](#ingerer-des-logs)
* [Examiner les règles de détection](#examiner-les-regles-de-detection)
* [Explorer les signaux de sécurité](#explorer-les-signaux-de-securite)

## Ingérer des logs

Si vous disposez déjà d'une source de journalisation, suivez la procédure de [mise en service au sein de l'application][1] pour commencer à recueillir des logs à partir de cette source.

La [documentation relative à la collecte de logs][2] de Datadog décrit en détail comment recueillir des logs à partir de nombreuses sources différentes pour les envoyer à Datadog. Dans un premier temps, tous les logs ingérés sont parsés et enrichis. Les règles de détection sont appliquées en temps réel à tous les logs traités, ceci afin d'optimiser la couverture de détection tout en éliminant les problèmes de performance ou de coût habituels liés à l'indexation de l'intégralité des données de log. [En savoir plus sur la fonctionnalité Logging without Limits™ de Datadog][3].

{{< img src="security_platform/security_monitoring/getting_started/ingest_logs_overview.png" alt="Ingérer des logs" >}}

## Examiner les règles de détection

Datadog propose des [règles de détection][4] par défaut, vous permettant ainsi de détecter immédiatement les menaces dans votre environnement. Ces règles détectent les menaces en fonction des bonnes pratiques connues. Si votre organisation nécessite une sécurité plus avancée, vous avez la possibilité d'activer d'autres règles afin de détecter les menaces plus poussées. Des modèles avancés sont également proposés pour vous permettre de détecter les menaces dans vos applications personnalisées. Consultez la [documentation relative aux règles de détection][5] pour en savoir plus.

## Explorer les signaux de sécurité

Lorsqu'une menace est détectée par une règle de détection, un signal de sécurité est généré. Les signaux de sécurité peuvent être mis en corrélation et triés dans le [Security Signals Explorer][6]. Consultez la documentation relative au [Security Signals Explorer][7] pour en savoir plus. 

## Pour aller plus loin

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/security/onboarding
[2]: /fr/logs/log_collection/
[3]: https://www.datadoghq.com/blog/logging-without-limits/
[4]: /fr/security_monitoring/default_rules/
[5]: /fr/security_monitoring/detection_rules/
[6]: https://app.datadoghq.com/security
[7]: /fr/security_monitoring/explorer/