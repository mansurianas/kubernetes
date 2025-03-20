# Kubernetes CronJob

A **CronJob** in Kubernetes allows you to run tasks automatically at specific times, similar to the Unix `cron` command. It helps automate repetitive tasks by scheduling jobs to run at regular intervals.

## Key Features of CronJobs

- **Scheduled Execution**: Set when the job should run, like every hour, daily, or weekly, using a simple time format.
  
- **Job Creation**: Each time the CronJob runs, it creates a new job, allowing you to have multiple jobs running at different times.

- **Automatic Cleanup**: You can configure rules to automatically delete old jobs after they finish, keeping your cluster organized.

- **Flexible Scheduling**: Use standard cron syntax to create complex schedules, such as running a job every Monday at 3 PM or every 5 minutes.

## Use Cases for CronJobs

- **Backups**: Schedule regular backups of databases or application data.
  
- **Report Generation**: Automatically create and send reports at set intervals.

- **Cleanup Tasks**: Run scripts to delete temporary files or old data periodically.

- **Data Processing**: Schedule tasks for data processing, like ETL (Extract, Transform, Load) jobs.

## How CronJobs Work

1. **Schedule Definition**: Define when the job should run using cron syntax.
  
2. **Job Creation**: At the scheduled time, Kubernetes creates a new job based on the CronJob definition.

3. **Job Execution**: The job runs the specified task, which can be anything from executing a script to processing data.

4. **Monitoring**: Kubernetes monitors the job to ensure it completes successfully. If it fails, you can check the logs for troubleshooting.

## Understanding the Schedule

The schedule for a CronJob is defined using a format similar to Unix cron jobs, which consists of five fields:

```plaintext
* * * * *
| | | | |
| | | | +---- Day of the week (0 - 7) (Sunday is both 0 and 7)
| | | +------ Month (1 - 12)
| | +-------- Day of the month (1 - 31)
| +---------- Hour (0 - 23)
+------------ Minute (0 - 59)


```
Example Schedules
0 0 * * *: Runs the job every day at midnight.
*/5 * * * *: Runs the job every 5 minutes.
30 14 * * 1: Runs the job at 2:30 PM every Monday.



```yml
kind: CronJob
metadata:
  name: minute-backup
  namespace: nginx
spec:
  schedule: "* * * * *"  # This means the job will run every minute
  jobTemplate:
    spec:
      template:
        metadata:
          name: minute-backup
          labels:
            app: minute-backup
        spec:
          containers:
            - name: backup-container
              image: busybox
              command:
                - sh
                - -c
                - >
                  echo "Backup Started" ;
                  mkdir -p /backups &&
                  mkdir -p /demo-data &&
                  cp -r /demo-data /backups &&
                  echo "Backup completed" ;
              volumeMounts:
                - name: data-volume
                  mountPath: /demo-data
                - name: backup-volume
                  mountPath: /backups
          restartPolicy: OnFailure
          volumes:
            - name: data-volume
              hostPath:
                path: /demo-data
                type: DirectoryOrCreate
            - name: backup-volume
              hostPath:
                path: /backups
                type: DirectoryOrCreate



```



Apply the CronJob Configuration

```
kubectl apply -f minute-backup-cronjob.yaml


```
kubectl get cronjobs -n nginx

```
kubectl get cronjobs -n nginx


```

```
NAME            SCHEDULE         SUSPEND   ACTIVE   LAST SCHEDULE   AGE
minute-backup   * * * * *       False     0       <none>          1m

```
To see the pods created by the jobs

``` 

kubectl get pods -n nginx



