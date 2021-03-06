node {
    sh "rm -rf $WORKSPACE/*"
    checkout scm
    
    stage('Build')
    {
        sh "chmod u+rwx $WORKSPACE/*.sh"
        sh "$WORKSPACE/build.sh"
    }
    stage('Test')
    {
        try
        {
            // Start target service
            sh "$WORKSPACE/test.sh"
        }
        catch(err)
        {
            junit '*.xml'
            sh "killall python || true"

            throw err
        }
    }
    stage('Peach API Security')
    {
        try
        {
            sh  "PEACH_AUTOMATION_CMD=\"pytest --peach=on tests.py\" " +
                "PEACH_PROFILE=Quick " +
                "PEACH_CONFIG=/opt/sdk/testrunners/custom/python/peach-web.project " +
                "PEACH_API=http://127.0.0.1:5000 " +
                "PEACH_UI=http://52.52.169.252:5000 " +
                "PEACH_JUNIT=$WORKSPACE/peach-web_test_target.xml " +
                "PEACH_VERBOSE=False "+
                "$WORKSPACE/peach.sh"
        }
        catch(err)
        {
            junit healthScaleFactor: 100.0, testResults: '*.xml'
            throw err
        }
        finally
        {
            sh "killall python || true"
        }
    }
    stage('Deploy')
    {
        echo "Deploying..."
    }
}
