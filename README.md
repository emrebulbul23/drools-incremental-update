# Drools Incremental KieBase/KieSession Update PoC

This small Proof of Concept shows the **JBoss BRMS/Drools** KieBase/KieSession incremental update feature.

## Incremental Updates
Drools provides the functionality to incrementally update the KieBase **and** running KieSessions created from that KieBase.
What this means is that Drools allows one to, at runtime, update the rules in a KieBase without having to restart the application.
It even allows to update rules in (long-)running KieSessions, which, for example, might be used in Complex Event Processign (CEP) scenario's.
This functionality is provided by the `updateToVersion(ReleaseId version)` method of the `KieContainer`. This functionality does
not explicitly require a `KieScanner` to be used, the functionality can also be used by directly using the `KieContainer` API,
as is shown in the unit-tests of this PoC.

## The PoC
This PoC is quite basic. It provides a (simple) domain/fact model, consisting of a single fact called [SimpleEvent](drools-incremental-update/src/main/java/org/jboss/ddoyle/drools/demo/model/v1/SimpleEvent.java).
This model, in combination with the various Drools *drl* rule defintion files, is used in the various JUnit tests that test the diffferent scenarios. Each JUnit test tests a different scenario, for example 
adding rules, changing rules, deleting rules, etc. These tests can be found in the [src/test/java](drools-incremental-update/src/test/java) directory. All tests contain a small JavaDoc comment that explains
the purpose of the test and the expected semantics and outcome.

To run the tests, execute `mvn clean test` in the root directory of the project.

## The incremental update semantics
We will now explain the behaviour of the incremental update of rules in a running `KieSession`.

### Adding rules
* When a `KieBase` is changed by adding a new rule to an existing DRL file (e.g. not changing the name of the DRL), the existing rules will **not** refire for facts/events that are in the KieSession. The new rule however will fire for **all** facts/events that are already
in the KieSession and that match the rule. E.g. if you have inserted 2 facts/events into the `KieSession`, and after that you add a rule that creates a match for both fatcs/events, the new rule will fire twice
on the next call to `KieSession.fireAllRules()`. Existing rules will not re-fire.
* When a 'KieBase' is changed by adding a new rule **and** changing the name of the DRL file, the existing rules **and** the new rules will (re)fire for all facts/evens in the KieSession (see this [test](drools-incremental-update/src/test/java/org/jboss/ddoyle/drools/demo/KieSessionRulesIncrementalUpdateAddedRulesTest.java#L89)). Drools will treat rules in a new DRL file as new rules, even if only the DRL file was renamed. This is 
also demonstrated in [this test](drools-incremental-update/src/test/java/org/jboss/ddoyle/drools/demo/KieSessionRulesIncrementalUpdateDifferentDrlTest.java).





## Interesting links:
* [The Drools project](http://www.drools.org)
* [The JBoss BRMS platform](http://www.redhat.com/en/technologies/jboss-middleware/business-rules)

