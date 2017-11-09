node {

    // doublecheck all required environment variables are set
    def expectedEnvVars = ["MODULE_PATH", "PRIV_DOCKER_REGISTRY", "DOCKER_PROJECT"]

    stage('Init') { // for display purposes
    
        echo '\nPreparing environment...'
        env.NODE_HOME = tool('Default NodeJS');
        env.PATH = toPath(env.NODE_HOME, "bin") + ":" +  env.PATH;

        checkVariables(expectedEnvVars);
        displayVariables(expectedEnvVars);
    }

    stage('Checkout') { // for display purposes
        echo '\nGetting source code...'
        checkout scm
    }

	dir("${env.MODULE_PATH}") {
        echo '\nBuilding NodeJS application ...'
	    stage('Build') {
	         shell ("npm", "install")
	    }
	
	    stage('Run tests') {
	        // Run the node build
	        shell("npm", "test")
	    }
	
	    stage('Archive results') {
            echo '\nArchiving artefacts ...'
//	        archive 'target/*.jar'
	    }

        docker.withRegistry("https://${env.PRIV_DOCKER_REGISTRY}", env.PRIV_DOCKER_REGISTRY_CRED_ID) {
            echo '\nCreating docker image ...'
            def app
            stage('Build image') {
                app = docker.build("${env.PRIV_DOCKER_REGISTRY}/${env.DOCKER_PROJECT}", ".")
            }

            stage('Push image') {
                app.push()
            }
        }
    }
}
