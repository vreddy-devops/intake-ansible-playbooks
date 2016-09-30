node {
    ws {
        checkout scm
        echo "${IMAGE_TAG}"

        withEnv(["AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}", 
            "AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}", 
            "AWS_DEFAULT_REGION=${AWS_DEFAULT_REGION}",
            "DOCKER_EMAIL=${DOCKER_EMAIL}",
            "DOCKER_IMAGE=${IMAGE_TAG}",
            "ANSIBLE_HOST_KEY_CHECKING=False"
            ]) {

            withCredentials([
                [$class: 'FileBinding', credentialsId: 'ssh_private_key', variable: 'KEY_FILE'],
                [$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker_credentials',
                            usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD']
            ]) {
                sh 'ansible-galaxy install -p ./roles dochang.docker'
                sh 'ansible-galaxy install -p ./roles Datadog.datadog'

                writeFile file: './hosts', text: HOSTS
                writeFile file: './group_vars/all', text: GROUP_VARS

                sh 'ansible-playbook -u ec2-user -i ./hosts --key-file=$KEY_FILE --skip-tags=local_dependencies --extra-vars="intake_image_tag=$DOCKER_IMAGE username=$DOCKER_USER email=$DOCKER_EMAIL password=$DOCKER_PASSWORD" config-docker-intake-node.yml'
            }
        }
    }
}