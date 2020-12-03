/*
Plugins:
    https://plugins.jenkins.io/metrics
    https://plugins.jenkins.io/blueocean

Script Approval:
    field io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper timingInfo
    method com.cloudbees.workflow.rest.external.RunExt getPauseDurationMillis
    method hudson.model.Actionable getAction java.lang.Class
    method hudson.model.Job getBuildByNumber int
    method io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper getDisplayName
    method io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper getStatus
    method io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper getType
    method io.jenkins.blueocean.rest.impl.pipeline.NodeGraphBuilder getPipelineNodes
    method io.jenkins.blueocean.rest.impl.pipeline.NodeRunStatus getResult
    method io.jenkins.blueocean.rest.impl.pipeline.NodeRunStatus getState
    method jenkins.metrics.impl.TimeInQueueAction getQueuingDurationMillis
    method jenkins.model.Jenkins getItemByFullName java.lang.String
    method org.jenkinsci.plugins.workflow.pipelinegraphanalysis.TimingInfo getPauseDurationMillis
    method org.jenkinsci.plugins.workflow.pipelinegraphanalysis.TimingInfo getStartTimeMillis
    method org.jenkinsci.plugins.workflow.pipelinegraphanalysis.TimingInfo getTotalDurationMillis
    new com.cloudbees.workflow.rest.external.RunExt
    new io.jenkins.blueocean.rest.impl.pipeline.PipelineNodeGraphVisitor org.jenkinsci.plugins.workflow.job.WorkflowRun
    staticField io.jenkins.blueocean.rest.impl.pipeline.FlowNodeWrapper$NodeType STAGE
    staticMethod jenkins.model.Jenkins getInstance
*/


import io.jenkins.blueocean.rest.impl.pipeline.*
import static groovy.json.JsonOutput.*
import com.cloudbees.workflow.rest.external.*

def jobName = currentBuild.projectName


node('master') {
    stage('stage 1') {
        sleep(0.3)
    }

    stage('stage 2') {
        sleep(0.3)
    }

    stage('stage 3') {
        sleep(0.3)
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
    def getQueuingDurationMillis = action.getQueuingDurationMillis()
    def pauseDurationMillis = runner.getPauseDurationMillis()

    pipelineInfo = [
        "jenkins_pipeline_info": [
            "id": currentBuild.number,
            "name": currentBuild.displayName,
            "status": currentBuild.currentResult,
            "start_time_millis": currentBuild.startTimeInMillis,
            "end_time_millis": currentBuild.startTimeInMillis + currentBuild.duration,
            "duration_millis": currentBuild.duration,
            "queue_duration_millis": getQueuingDurationMillis,
            "pause_duration_millis": pauseDurationMillis,
            "stages": stagesInfo
        ]
    ]
    println prettyPrint(toJson(pipelineInfo))
}