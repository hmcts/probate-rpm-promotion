#!groovy

@Library('Reform')
import uk.gov.hmcts.Artifactory
import uk.gov.hmcts.Packager
import uk.gov.hmcts.RPMTagger

def artifactory = new Artifactory(this)
def packager = new Packager(this, 'probate')

properties([
        [$class: 'GithubProjectProperty', displayName: 'Probate promotion pipeline', projectUrlStr: 'https://github.com/hmcts/probate-rpm-promotion.git'],
        parameters([
                string(description: 'probate frontend RPM Version', name: 'frontendRpmVersion'),
                string(description: 'business-service RPM Version', name: 'businessServiceWebRpmVersion'),
                string(description: 'submit-service RPM Version', name: 'submitServiceRpmVersion'),
                string(description: 'persistence-service RPM Version', name: 'persistenceServiceRpmVersion'),
                string(description: 'SOL-CCD-service RPM Version', name: 'solCcdRpmVersion')
        ])
])

node {
    try {
        stage('Tag testing passed'){
            new RPMTagger(this, 'frontend', packager.rpmName('frontend', params.frontendRpmVersion), 'probate-local').tagTestingPassedOn('test')
            new RPMTagger(this, 'business-service', packager.rpmName('business-service', params.businessServiceWebRpmVersion), 'probate-local').tagTestingPassedOn('test')
            new RPMTagger(this, 'submit-service', packager.rpmName('submit-service', params.submitServiceRpmVersion), 'probate-local').tagTestingPassedOn('test')
            new RPMTagger(this, 'persistence-service', packager.rpmName('persistence-service', params.persistenceServiceRpmVersion), 'probate-local').tagTestingPassedOn('test')
            new RPMTagger(this, 'sol-ccd-service', packager.rpmName('sol-ccd-service', params.solCcdRpmVersion), 'probate-local').tagTestingPassedOn('test')
        }

        stage('Promote to Prod') {
            deleteDir()
            artifactory.promoteLatestEligibleRPMtoProductionRepo('probate', 'frontend')
            artifactory.promoteLatestEligibleRPMtoProductionRepo('probate', 'business-service')
            artifactory.promoteLatestEligibleRPMtoProductionRepo('probate', 'submit-service')
            artifactory.promoteLatestEligibleRPMtoProductionRepo('probate', 'persistence-service')
            artifactory.promoteLatestEligibleRPMtoProductionRepo('probate', 'sol-ccd-service')
        }
    } catch (Throwable err) {
        slackSend(
                channel: '#probate-jenkins',
                color: 'danger',
                message: "promotion To Production Artifactory Has FAILED")
        throw err
    }
}
