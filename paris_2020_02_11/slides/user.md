# Utilisateur

Note:
  * Peu de connaissance Jenkins
  * 


## Pipeline Visualisation

![package](./images/package.png)


## Regle

1. Zero logique             <!-- .element: class="fragment" data-fragment-index="1" -->
2. Zero dependance          <!-- .element: class="fragment" data-fragment-index="2" -->
2. Makefile                 <!-- .element: class="fragment" data-fragment-index="3" -->
3. Script Bash              <!-- .element: class="fragment" data-fragment-index="4" -->
4. Script Python/Ruby/etc.  <!-- .element: class="fragment" data-fragment-index="5" -->


## Pipeline Declaratif

Jenkinsfile
```
pipeline {
 agent {
   kubernetes {
     label 'release-linux'
     yamlFile 'PodTemplates.d/release-linux.yaml'
     inheritFrom 'jnlp-linux'
   }
 }

 environment {
   RELEASE_PROFILE               = 'experimental'
   GPG_PASSPHRASE                = credentials('release-gpg-passphrase')
   MAVEN_REPOSITORY_USERNAME     = credentials('maven-repository-username')
   MAVEN_REPOSITORY_PASSWORD     = credentials('maven-repository-password')
   SIGN_STOREPASS                = credentials('signing-cert-pass')
 }

 stages {
   stage('Clone Jenkins Git Repository') {
     steps {
       container('jnlp') {
         sshagent(['release-key']) {
           sh 'utils/release.sh --cloneJenkinsGitRepository'
         }
       }
     }
   }
 }
}
```


## Pipeline Scripte

Jenkinsfile
```
#!groovy
buildPlugin(findbugs: [run: true, archive: true], checkstyle: [run: true, archive: true])
```
