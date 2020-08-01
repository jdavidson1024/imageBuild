

node () {

env.IMAGE_OUTPUT_DIR = "/var/lib/jenkins/packer_output/enterpriseLinux7/$BUILD_TAG"

deleteDir()

      stage ('Checkout Build Code') {
         checkout scm
       }

      stage ('Build Image'){
        sh '/usr/bin/packer.1.6.0 build --color=false -var-file=build_version-7.4.json -only=CentOS enterprise_linux2.json'
      }

      stage ('Package Image(s)')
      {
        //sh 'ovftool --shaAlgorithm=SHA1 $IMAGE_OUTPUT_DIR/output-vmware-iso-rhel/RHEL-7.4.vmx $IMAGE_OUTPUT_DIR/output-vmware-iso-rhel/RHEL-7-4.ova'
        sh 'ovftool --shaAlgorithm=SHA1 $IMAGE_OUTPUT_DIR/output-vmware-iso-centos/CentOS-8.2.vmx $IMAGE_OUTPUT_DIR/output-vmware-iso-centos/CentOS-8-2.ova'

      }

  if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'Development') {
        withCredentials([usernamePassword(credentialsId: 'bc5fc175-2beb-48f2-ac08-cd9b7bad8471',
          usernameVariable: 'nexusUSERNAME', passwordVariable: 'nexusPASSWORD'),
          usernamePassword(credentialsId: '45f8fe97-3c28-4cdb-b105-235612834ff2',
          usernameVariable: 'vSphereUSERNAME', passwordVariable: 'vSpherePASSWORD')
          ])
        {
          stage ('Upload Image(s) to Nexus')
          {
            //sh 'curl -k -v -u $nexusUSERNAME:$nexusPASSWORD --upload-file $IMAGE_OUTPUT_DIR/output-vmware-iso-rhel/RHEL-7-4.ova https://nexus.int.sentania.net/repository/labRepo/lab/enterpriseLinux/RHEL-7-4-$BUILD_TAG.ova'
            sh 'curl -k -v -u $nexusUSERNAME:$nexusPASSWORD --upload-file $IMAGE_OUTPUT_DIR/output-vmware-iso-centos/CentOS-8-2.ova http://192.168.11.136:8081/repository/labRepo/LinuxBuilds/Iso/CentOS-8-2-$BUILD_TAG.ova'
          }
          stage ('Upload Image(s) to vsphere')
          {
            sh '/bin/pwsh deploy-image.ps1 -outputdir $IMAGE_OUTPUT_DIR -BUILD_TAG $BUILD_TAG -GIT_BRANCH $JOB_NAME -vSphereUSERNAME $vSphereUSERNAME -vSpherePASSWORD $vSpherePASSWORD'
          }
        }

          stage ('Clean packer output files')
          {
            sh 'rm -rf $IMAGE_OUTPUT_DIR'
          }
    }
}
