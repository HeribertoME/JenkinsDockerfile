FROM jenkins/jenkins:2.426

## Disable startup wizard
RUN echo 2.0 > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state

## Install custom plugins
COPY --chown=jenkins:jenkins ./plugins.txt /usr/share/jenkins/plugins.txt
RUN jenkins-plugin-cli --plugin-file=/usr/share/jenkins/plugins.txt

# because of /var/jenkins_home/.ssh/known_hosts [SSH] No Known Hosts file was found at /var/jenkins_home/.ssh/known_hosts. Please ensure one is created at this path and that Jenkins can read it.
RUN mkdir -p /var/jenkins_home/.ssh/ && touch /var/jenkins_home/.ssh/known_hosts && chown -R jenkins:jenkins /var/jenkins_home/.ssh/