stage 'build'
node {
    checkout scm
    mvn 'clean compile'
    stash excludes: '', includes: '**', name: 'source'
}

stage 'test'
parallel 'integration': {
    node {
        unstash 'source'
        mvn 'test'
        step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
    }
}, 'quality': {
    node {
        unstash 'source'
        mvn 'test findbugs:findbugs -DskipTests'
        step([$class: 'FindBugsPublisher', pattern: '**/findbugsXml.xml'])
    }
}

stage name: 'package', concurrency: 1
node {
    unstash 'source'
    mvn 'package -Dmaven.test.skip'
    archive '**/*.war'
}

def mvn(args) {
//    sh "${tool 'Maven'}/bin/mvn ${args} -Dmaven.repo.local=${WORKSPACE}/.m2"
    sh "${tool 'Maven'}/bin/mvn ${args}"
}
