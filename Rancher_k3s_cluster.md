# Rancher k3s cluster
Created Monday 26 August 2024

REF: <https://github.com/diademiemi/homelab>

Homelab Project
---------------
This is my homelab project. It is split across a NAS at home and a dedicated server rented at Hetzner. These run the applications I self-host.

The local NAS is used to store media and backups, and runs a Jellyfin instance to serve media. The dedicated server at Hetzner runs 4 VMs, each running a Rancher cluster. The Rancher cluster is completely deployed with Ansible and Terraform. The Rancher clusters are configured and standardised with Fleet. Workloads for the clusters are deployed with ArgoCD, Helm and Kustomize.

Motivation
----------
I have deployed this homelab to learn more about Kubernetes, Rancher, GitOps and general DevOps practices. I leaned into the Rancher ecosystem as it is a very easy to use Kubernetes distribution with a relatively low overhead for its featureset. I have tried to explore as many of the features of Rancher as possible, such as Fleet, ArgoCD, Longhorn, OPA Gatekeeper, CIS scans and more in preparation for exams and certifications related to these technologies, but also out of personal interest.

I have learnt a lot about Rancher and have an overall very positive experience with it, I feel confident to recommend it to others. I believe I have learnt a lot about Rancher, Kubernetes and DevOps practices in general during this project.

My goal was to have a homelab that is fully deployed with code, and can be deployed again with minimal effort. I believe I have achieved this goal, and I am very happy with the result. I have reset the cloud server dozens of times, and it is always back up and running within half an hour without me needing to do anything aside from running a single Ansible playbook.

