node {
    env.NODEJS_HOME = "${tool 'nodejs-22-6-0'}"
    env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"

    properties([
        disableConcurrentBuilds(abortPrevious: true) 
    ])

    stage ('Checkout') {
        checkout scm
    }

    wrap([$class: 'TimestamperBuildWrapper']) {
        stage('Installing Dependencies') {
            cache(maxCacheSize: 550, caches: [
                arbitraryFileCache(
                    cacheName: 'npm-dependecny-cache', 
                    cacheValidityDecidingFile: 'package-lock.json', 
                    includes: '**/*', 
                    path: 'node_modules')
                    ] ) {
                        sh 'node -v'
                        sh 'npm install --no-audit'
                        stash(includes: 'node_modules/', name: 'solar-system-node-modules')
                    }
            }
    }    
}