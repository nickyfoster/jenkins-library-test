// https://plugins.jenkins.io/metrics
// https://plugins.jenkins.io/blueocean

import io.jenkins.blueocean.rest.impl.pipeline.*
import static groovy.json.JsonOutput.*
import com.cloudbees.workflow.rest.external.*

def jobName = currentBuild.projectName


node('master') {
    stage('sleep') {
        sleep(0.8)
    }

    stage('get pipe info') {
        def info = currentBuild.toString()
        println(info)
    }

    stage('last stage') {
        def build = currentBuild.currentResult
        // println(build)
        def instance = Jenkins.getInstance()
        def pipe = instance.getItemByFullName(jobName)
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

    def run = Jenkins.instance.getItemByFullName(jobName).getBuildByNumber(currentBuild.number)
    PipelineNodeGraphVisitor visitor = new PipelineNodeGraphVisitor(run)
    def stageNodes = visitor.getPipelineNodes().findAll { it.getType() == FlowNodeWrapper.NodeType.STAGE }
    def stagesInfo = []

    for (def node: stageNodes) {
        String stageName = node.getDisplayName()
        stagesInfo.add(["name": node.getDisplayName(),
                        "status": "${node.status.result} (${node.status.state})",
                        "start_time_millis": node.timingInfo.startTimeMillis,
                        "duration_millis": node.timingInfo.totalDurationMillis,
                        "pause_duration_millis": node.timingInfo.pauseDurationMillis])
    }

    def action = Jenkins.instance.getItemByFullName(jobName).getBuildByNumber(currentBuild.number).getAction(jenkins.metrics.impl.TimeInQueueAction)
    RunExt runner = new RunExt()

    pipelineInfo = [
        "jenkins_pipeline_info": [
            "id": null,
            "name": null,
            "status": currentBuild.currentResult,
            "start_time_millis": currentBuild.startTimeInMillis,
            "end_time_millis": currentBuild.startTimeInMillis + currentBuild.duration,
            "duration_millis": currentBuild.duration,
            "queue_duration_millis": action.getQueuingDurationMillis(),
            "pause_duration_millis": runner.getPauseDurationMillis(),
            "stages": stagesInfo
        ]
    ]
    println prettyPrint(toJson(pipelineInfo))
}