pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build WAR') {
      steps {
        sh '''
          chmod +x mvnw || true
          ./mvnw -DskipTests package || mvn -DskipTests package
          ls -la target || true
        '''
      }
    }

   stage('Build Docker Image') {
    steps {
      sh '''
        echo "== Target contents =="
        ls -la target
  
        cat > Dockerfile <<'EOF'
        FROM tomcat:9.0-jdk17
        RUN rm -rf /usr/local/tomcat/webapps/*
        COPY target/vehiculosBuild.war /usr/local/tomcat/webapps/ROOT.war
        EXPOSE 8080
        EOF
  
        docker build -t vehiculos_tomcat:latest .
        echo "== Docker images =="
        docker images | head
      '''
    }
  }

stage('Deploy') {
      steps {
        sh '''
          docker stop contenedor_sucursal || true
          docker rm contenedor_sucursal || true
          docker run -d -p 9090:8080 --name contenedor_sucursal vehiculos_tomcat:latest
          docker ps
        '''
      }
    }
  }
}
