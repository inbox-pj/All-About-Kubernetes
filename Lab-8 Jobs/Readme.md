# LAB-8 Jobs in Kubernates

### How to run jobs
Jobs are a construct that run a pod once, and then stop. However, unlike pods in deployments, 
the output of the job is kept around until you decide to remove it.

Running a job is similar to running a deployment, and we can create this by `kubectl create -f lab-08-job-01.yaml`

To see the output of the job: `kubectl get jobs`

You can find the pod that ran by doing a `kubectl get pods`, and then get the logs from it as well.

### How to run cron jobs
Cron jobs are like jobs, but they run periodically.

Start your cron by running `kubectl create -f lab-08-cron-02.yaml`

We can use the cronjob api to view your cronjobs: `kubectl get cronjobs`. It adds the last schedule date

to edit the cronjob:    `kubectl edit cronjob lab-08-cron-02 o=yaml`

