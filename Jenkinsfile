pipeline {
    agent any
    environment {
        QUAY_REPO = "quay.io/vaibhava_srivastava"
        OPENSHIFT_NAMESPACE = "amq-streams-kafka"
        KAFKA_BOOTSTRAP_SERVERS = "my-cluster-kafka-listener1-bootstrap-amq-streams-kafka.apps.cluster-9mfh6.dynamic.redhatworkshops.io:443"
    }
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Build & Push Microservices") {
            steps {
                script {
                    sh "oc start-build -F order-service --from-dir=order-service"
                    sh "oc start-build -F stock-service --from-dir=stock-service"
                    sh "oc start-build -F email-service --from-dir=email-service"
                }
            }
        }

        stage("Deploy Microservices") {
            steps {
                script {
                    sh "oc rollout latest dc/order-service -n ${OPENSHIFT_NAMESPACE}"
                    sh "oc set env dc/order-service -n ${OPENSHIFT_NAMESPACE} KAFKA_BOOTSTRAP_SERVERS=${KAFKA_BOOTSTRAP_SERVERS}"

                    sh "oc rollout latest dc/stock-service -n ${OPENSHIFT_NAMESPACE}"
                    sh "oc set env dc/stock-service -n ${OPENSHIFT_NAMESPACE} KAFKA_BOOTSTRAP_SERVERS=${KAFKA_BOOTSTRAP_SERVERS}"

                    sh "oc rollout latest dc/email-service -n ${OPENSHIFT_NAMESPACE}"
                    sh "oc set env dc/email-service -n ${OPENSHIFT_NAMESPACE} KAFKA_BOOTSTRAP_SERVERS=${KAFKA_BOOTSTRAP_SERVERS}"
                }
            }
        }
    }
}
