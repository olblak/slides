# Administrateur

Note:
* Toute personne amenée à deployer ou maintenir Jenkins


## Requiert

**Système de fichier** | **Mémoire** | **CPU**

Note:
  docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts


## Configuration

* Générale
* Tâche
* Secret
* Agent
 * Outils


## Générale
* Init Groovy Script
* JobDsl
* config.xml
* Configuration As Code


## Init Groovy

```groovy
#!/usr/bin/env groovy

import jenkins.model.*
import hudson.security.*
import hudson.security.csrf.*
import hudson.model.Item

def instance = Jenkins.instance

instance.numExecutors = 0

println "Checking CSRF protection..."
if (instance.crumbIssuer == null) {
  println "Enabling CSRF protection"
  instance.crumbIssuer = new DefaultCrumbIssuer(true)
}

println 'Checking authentication status'
if (instance.securityRealm == SecurityRealm.NO_AUTHENTICATION) {
  println "Enabling LDAP-based authentication"

  LDAPSecurityRealm realm  = new LDAPSecurityRealm(
    "<%= @ldap_url %>",
    "<%= @ldap_dn %>",
    "ou=people", /* hard-coding this to our schema */
    "cn={0}",    /* -- */
    "ou=groups", /* -- */
    "",          /* -- */
    new jenkins.security.plugins.ldap.FromGroupSearchLDAPGroupMembershipStrategy(''),
    "<%= @ldap_admin_dn %>",
    new hudson.util.Secret("<%= @ldap_admin_password %>"),
    false,
    false,
    new LDAPSecurityRealm.CacheConfiguration(100, 300), /* cache 100 items for 5 minutes */
    null, /* no environmentProperties */
    "displayname", /* hard-coding this to our schema */
    "email",       /* -- */
    IdStrategy.CASE_INSENSITIVE,
    IdStrategy.CASE_INSENSITIVE,
  )

  instance.securityRealm = realm
}

/* Every time we run, it's worth re-defining our authorization strategy to make
 * sure that it is correct
 */
AuthorizationStrategy strategy = new GlobalMatrixAuthorizationStrategy()

<%if @anonymous_access %>
[
    Jenkins.READ,
    Item.READ,
].each { permission ->
    strategy.add(permission, 'anonymous')
}
<%end%>

<% @admin_ldap_groups.each do |group|%>
strategy.add(Jenkins.ADMINISTER, '<%= group %>')
<%end%>

instance.authorizationStrategy = strategy

instance.save()
```


## Tache - JobDSL

```
multibranchPipelineJob('core-package') {
  displayName "Core Package"
  description "Jenkins Core Packaging"
  branchSources {
    github {
      id('2019092401')
      scanCredentialsId('github')
      repoOwner('jenkins-infra')
      repository('release')
      includes('master')
    }
  }
  factory {
    workflowBranchProjectFactory {
      scriptPath('Jenkinsfile.d/package')
    }
  }
}
```


## config.xml

![XML](./images/xml.png)  


## Configuration As Code 

```yaml
jenkins:
  securityRealm:
    ldap:
      configurations:
        - server: "${LDAP_SERVER}"
          rootDN: "${LDAP_ROOT_DN}"
          managerDN: "${LDAP_MANAGER_DN}"
          managerPasswordSecret: "${LDAP_MANAGER_PASSWORD}"
          userSearch: cn={0}
      cache:
        size: 100
        ttl: 300
  authorizationStrategy:
    globalMatrix:
      grantedPermissions:
        - "Overall/Administer:release-core"
        - "Overall/Read:release-core-dev"
        - "Job/Build:release-core-dev"
        - "Job/Cancel:release-core-dev"
        - "Job/Read:release-core-dev"
```

[jenkinsci/configuration-as-code-plugin](https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos)


## Agents


## Jenkins Plugins


## &#9728;

* Ecosystem
* Liberté Totale
 * Nom
 * Release
 * Maintenance


## &#9762;

* Liberté Totale
 * Nom
 * Release
 * Maintenance


![PluginSite](./images/pluginsite2.png)  

Note:
* Readme.md vs Confluence
** Pull Request reviews
* Maintainers
* Last Release
* Number of Installation
* Previous security Warning
* https://github.com/jenkinsci/jep/blob/master/jep/221/README.adoc


## Amelioration

* [Core Infrastructure Initiatives](https://bestpractices.coreinfrastructure.org/en/projects/3538)
* [JEP 221 - Automatisation des releases des plugins](https://github.com/jenkinsci/jep/blob/master/jep/221/README.adoc)


