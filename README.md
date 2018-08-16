# Shibboleth IdP v3: Audit logging extensions

[![License](http://img.shields.io/:license-mit-blue.svg)](https://opensource.org/licenses/MIT)
[![Build Status](https://travis-ci.org/mpassid/shibboleth-idp-audit-ext.svg?branch=master)](https://travis-ci.org/mpassid/shibboleth-idp-audit-ext)
[![Coverage Status](https://coveralls.io/repos/github/mpassid/shibboleth-idp-audit-ext/badge.svg?branch=master)](https://coveralls.io/github/mpassid/shibboleth-idp-audit-ext?branch=master)

## Overview

This module implements some audit logging extensions for [Shibboleth Identity Provider v3]
(https://wiki.shibboleth.net/confluence/display/IDP30/Home). More precisely, the module offers a way to
include the value of a specific attribute and/or used authentication flow identifier to the audit logging via
[AuditLoggingConfiguration](https://wiki.shibboleth.net/confluence/display/IDP30/AuditLoggingConfiguration).

## Prerequisities and compilation

- Java 7+
- [Apache Maven 3](https://maven.apache.org/)

```
mvn package
```

After successful compilation, the _target_ directory contains _idp-profile-impl-audit-\<version\>.jar_ file.

## Deployment

After compilation, the module's JAR-file must be deployed to the IdP Web
application. Depending on the IdP installation, the module deployment may be achieved for instance 
with the following sequence:

```
cp target/idp-profile-impl-audit-<version>.jar /opt/shibboleth-idp/edit-webapp/WEB-INF/lib
cd /opt/shibboleth-idp
sh bin/build.sh
```

The final command will rebuild the _war_-package for the IdP application.

After deployment, the module can be configured in the _/opt/shibboleth-idp/conf/audit.xml_ file. The following
example shows how the configuration of two new fields to *shibboleth.PostAssertionAuditExtractors* (see
[AuditLoggingConfiguration](https://wiki.shibboleth.net/confluence/display/IDP30/AuditLoggingConfiguration)):

* _%attrIdValue_: The value of the attribute _attributeId_.
* _%authnFlowValue_: The id of the the used authentication flow.

```
<bean id="shibboleth.PostAssertionAuditExtractors" parent="shibboleth.DefaultPostAssertionAuditExtractors" lazy-init="true">
    <property name="sourceMap">
        <map merge="true">
            <entry>
                <key>
                    <bean class="java.lang.String">
                        <constructor-arg value="attrIdValue"/>
                    </bean>
                </key>
                <bean class="fi.mpass.shibboleth.profile.audit.impl.AttributeValueAuditExtractor">
                    <constructor-arg value="attributeId"/>
                </bean>
            </entry>
            <entry>
                <key>
                    <bean class="java.lang.String">
                        <constructor-arg value="authnFlowValue"/>
                    </bean>
                </key>
                <bean class="fi.mpass.shibboleth.profile.audit.impl.AuthnFlowIdAuditExtractor"/>
            </entry>
        </map>
    </property>
</bean>
```
