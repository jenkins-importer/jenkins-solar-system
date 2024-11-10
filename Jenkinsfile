podTemplate(
    cloud: 'dasher-prod-k8s-us-east', 
    label: 'nodejs-pod', 
    containers: [
        containerTemplate(
            args: '9999999', 
            command: 'sleep', 
            image: 'node:18-alpine', 
            name: 'node-18', 
            privileged: true, 
            ttyEnabled: true),
        containerTemplate(
            args: '9999999', 
            command: 'sleep', 
            image: 'node:19-alpine', 
            name: 'node-19', 
            privileged: true, 
            ttyEnabled: true)])
{
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
            node('nodejs-pod') {
                container('node-18') {
                    checkout scm
                    withCredentials([usernamePassword(credentialsId: 'mongo-db-credentials', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                        sh 'node -v'
                        sh 'npm test' 
                    }
                }
            }
        }       
    }
}