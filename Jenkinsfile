node('ubuntu-docker-jdk17-node20') {
    env.NODEJS_HOME = "${tool 'nodejs-22-6-0'}"
    env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"
    env.MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"

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

    stage('Unit Testing') {
        withCredentials([usernamePassword(credentialsId: 'mongo-db-credentials', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
            sh 'node -v'
            sh 'npm test' 
        }
    }       
}