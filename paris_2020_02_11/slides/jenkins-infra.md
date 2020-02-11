# Infrastructure

Note:
  * Projet Ouvert 
  * https://github.com/jenkins-infra


## Etat Actuel

Quelque part en Californie, un homme, une machine...

Note:
* Dépendance forte sur une personne, une machine
* Difficile d'auditer, d'ameliorer


## Git à la rescousse

* Infrastructure => [jenkins-infra/azure](https://github.com/jenkins-infra/azure)
* Service        => [jenkins-infra/chart](https://github.com/jenkins-infra/charts)
* Procédure      => [jenkins-infra/release](https://github.com/jenkins-infra/release)


<!-- .slide: data-background="./images/tools.png" -->


## Infrastructure
[jenkins-infra/azure](https://github.com/jenkins-infra/azure)

* Kubernetes (Windows&Linux) - [kubernetes plugin](https://plugins.jenkins.io/kubernetes)
* Kubernetes Secret          - [kubernetes-credentials-provider](https://plugins.jenkins.io/kubernetes-credentials-provider)

Note: 
  ssh-agent
  windows & linux


## Service
[jenkins-infra/chart](https://github.com/jenkins-infra/charts)

* Helm Charts
 * stable/jenkins
 * jenkins
 * env-jenkins-release
* Helmfile
* Sops


## Service

```
+---------------------+                         +------------------------+
|release.ci.jenkins.io+------------------------>|release.repo.jenkins.io |
|      "release"      |                   +-----+                        |
+---------------------+                   |     +------------------------+
                                          |
                                          |
                                          |
+---------------------+<------------------+     +------------------------+
|release.ci.jenkins.io|                         |release.repo.jenkins.io |
|      "packaging"    |                         |                        |
+---------------------+------------------------>+------------------------+

```


## Release

![release](./images/release.png)


## Process

![package](./images/package.png)


<!-- .slide: data-background="./images/contribute_to.png" -->
