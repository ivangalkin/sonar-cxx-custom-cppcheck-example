see https://github.com/SonarOpenCommunity/sonar-cxx/issues/1573

see run.sh

```BASH

#!/bin/bash

# GOAL: create a custom sonar-cxx warning about the usage of unwanted (obsolescent/non thread-safe/error-prone etc) functions
#       this example demonstrates how to use sonar-cxx + cppcheck in order to warn e.g. about 'printf' usages
# 

# 0. create a custom cppcheck rule (see https://github.com/SonarOpenCommunity/sonar-cxx/wiki/Extending-the-code-analysis for more details)
#
# 1. go to <sonarserver>/admin/settings -> C++ (Community) -> Cppcheck custom rules (sonar.cxx.cppcheck.customRules)
#    insert the content of file custom_cppcheck_rules.xml here
#    (you might want to strip the XML first, since there is a length limitation
#    e.g. use https://www.freeformatter.com/xml-formatter.html with Indentation level == "Compact mode")
#
# 2. restart your server
#    http://localhost:9000/admin/system -> Restart Server
#
# 3. activate new cppcheck rules (make sure your project is associated with the right quality profile)
#    see https://github.com/SonarOpenCommunity/sonar-cxx/wiki/Activate-quality-rules
#    direct link for the example is
#    http://localhost:9000/coding_rules?open=cppcheck%3AprintfCalled&q=printf&repositories=cppcheck
#
# 4. execute cppcheck with custom library specification
#   (tested with cppcheck v1.84, CFG might be incompatible with older versions, please see documentation)

cppcheck --enable=all --suppress=missingIncludeSystem --library=custom_cppcheck_library.cfg --xml --xml-version=2 --output-file=cppcheck.xml src/

#    cppcheck.xml must contain several entries like
#
#    <error id="printfCalled" severity="portability" msg="Obsolescent function &apos;printf&apos; called. Applications are recommended to be rewritten to use C++ stream operators." verbose="Obsolescent function &apos;printf&apos; called. Applications are recommended to be rewritten to use C++ stream operators." cwe="477">
#      <location file="src/main.cpp" line="27"/>
#    </error>
#
# 5. execute the sonar-scanner
#    project properties must import the cppcheck.xml
#    sonar.cxx.cppcheck.reportPath=cppcheck.xml

sonar-scanner -Dproject.settings=sonar.properties


```
