environment {
    
  }

podTemplate(label: 'slave', containers: [
    containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2375 --storage-driver=overlay'),
    containerTemplate(name: 'helm', image: 'alpine/helm:latest', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'kubectl', image: 'gcr.io/cloud-builders/kubectl', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'terraform', image: 'hashicorp/terraform', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'azure', image: 'mcr.microsoft.com/azure-cli', command: 'cat', ttyEnabled: true),


  ],
  volumes: [
  emptyDirVolume(memory: false, mountPath: '/var/lib/docker'),
  hostPathVolume(mountPath: '/root/.azure', hostPath: '/root/.azure'),
  hostPathVolume(mountPath: '/root/.kube', hostPath: '/root/.kube'),

  ]) {

  node('slave') {

    stage('SCM checkout') {
      checkout scm

    }


        stage('Azure Access Management') {
              container('azure') {
                                    sh """
                                          rm -rf /root/.kube/config
                                          az login --service-principal -u <<SERVICE PRINCIPLE>> -p <<SERVICE PRINCIPLE PASSWORD>> --tenant <<TENANT ID>>

                                          az aks get-credentials --resource-group <<RESOURCE GROUP>> --name <<AKS CLUSTER NAME>>
                                              """

                                  }
            }

    stage('Build and Push image') {
      container('docker') {
        sh """
        docker login -u <<USER ID>> -p <<PASSWORD>> <<REGISTERY URL>>
        docker build -t <<REGISTERY URL>>/backend backend/
        docker push <<REGISTERY URL>>/backend 
        docker build -t <<REGISTERY URL>>/frontend frontend/
        docker push <<REGISTERY URL>>/frontend
        """

      }
    }

    stage('Deployment to AKS') {

          container('kubectl') {
            sh """
                      kubectl apply -f kubernetes/backend-deploy.yaml
                      kubectl apply -f kubernetes/backend-network.yaml
                      kubectl apply -f kubernetes/frontend-deploy.yaml
                      kubectl apply -f kubernetes/frontend-deploy.yaml
                      """

          }
        }
  }

}