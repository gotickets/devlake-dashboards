# Open source Grafana Dashboards for Apache DevLake

> **gotickets fork.** Trimmed to the dashboards we use: Homepage, Engineering Overview, DORA (overview, by team,
> validation, and the five detail boards), Jenkins, Jira, GitHub, Deployments (Apache DevLake's ArgoCD board at
> `v1.0.3-beta10`, renamed) and Issue Releases. Every board follows the Grafana time picker (default `now-6M` to `now`).

This repo contains open source dashboards that you can install on [Apache Devlake](https://devlake.apache.org) to get visibility and measure key aspects of your product and delivery process. 

These are the a subset of dashboards we use with clients in [The Polaris Advisor Program](https://polaris-advisor.com). 

Each dashboard comes with a guide on how and when to to use it, and more importantly the  underlying assumptions that you need to take into account when using the data in the dashboard to make decisions. 

We plan to open source more of the dashboards on an ongoing basis so check back occasionally or follow us for updates. 

To import these dashboards into your installation of Apache Devlake edit the json file and replace the name of the MySQL datasource, if you've changed it from the Devlake default (*mysql*).


The dashboards are provided under the same Apache 2.0 license as DevLake and may be used under the terms of the license. 

# How to use
If you new to Devlake, head over to the [Apache Devlake](https:devlake.apache.org) site and get a standard installation up and running first. 
Connect a few sources like a github repo, jira board etc and see that the data has been collected and populated. 

Once you are set up there, using our dashboard is just a matter of pulling a different image and replacing the grafana service to use it.

- `docker pull polarisadvisor/devlake-dashboards:latest`
- Replace the standard grafana image in `docker-compose.yml` with this image.
- Stop and restart the containers. 

The setup assumes that the dashboards connect to the datasource named `mysql` defined in the standard
grafana setup on devlake. 

You are also free to import individual dashboard json files into your current Devlake instance. 
. 




Check back for more updates. If you check it out, and find issues, please report them. 
