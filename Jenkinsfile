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

  stage 'Run Go tests'
  sh("docker run ${imageTag}")

  stage 'Push image to registry'
  sh("docker push ${imageTag}")
}
