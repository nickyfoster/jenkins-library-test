node('master') {
    stage('sleep') {
        // sleep(10)
    }

    stage('get pipe info') {
        def info = currentBuild.toString()
        println(info)
    }

    stage('last stage') {
        def build = currentBuild.currentResult
        // println(build)
        def instance = Jenkins.getInstance()
        def pipe = instance.getItemByFullName("test")
        // println(pipe)
        pipe.getBuilds().each{ b ->
            def cb = b.getNumber()
            if (currentBuild.number == b.getNumber()) {
                // println(b)
                def currentStatus = b.buildStatusSummary.message
                def cause = b.getCauses()[0]
                def user = cause instanceof Cause.UserIdCause? cause.getUserId():""
                println "Build: ${b} | Status: ${currentStatus} | Cause: ${cause} | User: ${user}"
                def parameters = b.getAction(ParametersAction)?.parameters
                parameters.each {
                    println "Type: ${it.class} Name: ${it.name}, Value: ${it.dump()}"
                }
            }
        }
    }
}