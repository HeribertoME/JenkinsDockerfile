FROM jenkins/jenkins:lts

## Disable startup wizard
RUN echo 2.0 > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state

## Install custom plugins
COPY --chown=jenkins:jenkins ./plugins.txt /usr/share/jenkins/plugins.txt
RUN jenkins-plugin-cli --plugin-file=/usr/share/jenkins/plugins.txt

# Copy jobs
COPY --chown=jenkins:jenkins ./FirstJob /usr/share/jenkins/ref/jobs/FirstJob
COPY --chown=jenkins:jenkins ./AndroidPipelineJob /usr/share/jenkins/ref/jobs/AndroidPipelineJob