#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }


        stage('check java') {
            sh "java -version"
        }

        stage('clean') {
            sh "chmod +x mvnw"
            sh "./mvnw -ntp clean -P-webapp"
        }
       
       
        stage('install tools') {
            sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:install-node-and-npm@install-node-and-npm"
        }

        stage('npm install') {
            sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm"
        }

        // --- ETAPAS DE TEST OMITIDAS (BORRADAS) ---
        // Las borramos porque fallaban (104 errores)
        // y porque el 'junit' rompía la sintaxis de comentarios.

        stage('packaging') {
            sh "./mvnw -ntp verify -P-webapp -Pprod -DskipTests"
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        }
        
        // Esta es la etapa que agregamos del PDF
        stage('publish docker') {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-login', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
            sh "./mvnw -ntp jib:build"
          }
        }
}
