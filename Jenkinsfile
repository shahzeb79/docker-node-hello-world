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
  
  stage "Clean Cluster"
  switch (env.BRANCH_NAME) {
    case "master":
        // Change deployed image in canary to the one we just built
        sh("kubectl delete deployment gceme-frontend-production --namespace production")
        sh("kubectl delete svc gceme-frontend --namespace production")
        break
  }
  stage "Deploy Application"
  switch (env.BRANCH_NAME) {
    case "master":
        // Change deployed image in canary to the one we just built
        sh("kubectl --namespace=production apply -f k8s/services/")
        sh("kubectl --namespace=production apply -f k8s/production/")
        sh("echo http://`kubectl --namespace=production get service/gceme-frontend --output=json | jq -r '.status.loadBalancer.ingress[0].ip'` > ${feSvcName}")
        break
  }
}
