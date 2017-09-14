node {
  def project = 'umapp-cluster'
  def appName = 'nodehello'
  def feSvcName = "${appName}-frontend"
  def imageTag = "shahzeb799/${appName}:latest"

  checkout scm

  stage 'Docker Login'
  sh("docker login --username=shahzeb799 --password=Shani@123")
  
  stage 'Build image'
  sh("docker build -t ${imageTag} .")

  stage 'Push image to registry'
  sh("docker push ${imageTag}")
  
  stage "Deploy Application"
  switch (env.BRANCH_NAME) {
    case "master":
        // Change deployed image in canary to the one we just built
        sh("kubectl --namespace=production apply -f k8s/services/")
        sh("kubectl --namespace=production apply -f k8s/production/")
        sh("echo Application is excesible on: http://`kubectl --namespace=production get service/gceme-frontend --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
        break
    case "canary":
        // Change deployed image in canary to the one we just built
        sh("kubectl --namespace=production apply -f k8s/services/")
        sh("kubectl --namespace=production apply -f k8s/production/")
        sh("echo http://`kubectl --namespace=production get service/${feSvcName} --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
        break

    // Roll out a dev environment
    default:
        // Create namespace if it doesn't exist
        sh("kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}")
        // Don't use public load balancing for development branches
        sh("sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml")
        sh("sed -i.bak 's#shahzeb799/gceme:1.0.0#${imageTag}#' ./k8s/dev/*.yaml")
        sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/services/")
        sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/dev/")
        echo 'To access your environment run `kubectl proxy`'
        echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${feSvcName}:80/"
  }
}