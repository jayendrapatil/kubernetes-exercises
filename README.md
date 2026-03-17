[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

# Kubernetes Exercises

This collection covers a set of exercises that are categorized topics wise and referred back to the individual Kubernetes certification exams.
As the exam pattern and topics keep on changing, however, the topics remain more or less the same, I have created the exercises per topics and mapped them back to the exam.

## Kubernetes Playground

Try out the Killercoda Kubernetes playgroud which provides 2 node Kubernetes cluster, which is good enough to complete almost all of the exercises.

[Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
[Killer.sh](https://killer.sh/) - provides a playground for CKA, CKAD, and CKS exams with a valid exam registration.

## Structure 

 - [Certified Kubernetes Administrator (CKA)](cka) covers topics for CKA exam.
 - [Certified Kubernetes Application Developer (CKAD)](ckad) covers topics for CKAD exam.
 - [Certified Kubernetes Security Specialist (CKS)](cks) covers topics for CKS exam.
 - [Data](data) provides any data required for the exercises.
 - [Topics](topics) covers individual topics.

## Exam Pattern & Tips

 - CKA/CKAD/CKS are open book test.
 - Exams keep on upgrading as per the latest Kubernetes version and is currently on 1.35
 - Exams require you to solve 15-20 questions in 2 hours.
 - Make use of imperative commands as much as possible, a couple examples:
    - `kubectl run nginx --image=nginx` create a pod instead of writing a manifest file and applying it.
    - `kubectl expose pod nginx --port=80` create a service which targets the pod just created, exposing a cluster IP service on port 80.
    - `kubectl create deployment my-nginx --image=nginx:1.23` create a deployment instead of writing a manifest file.
    - `kubectl set image deploy/my-nginx nginx=nginx:1.24` trigger a rolling update by changing the image of the nginx container in the my-nginx deployment to version 1.24.
 - The exam environment now includes the VSCodium editor. Previously you had an online notepad on the right corner to note down. I hardly used it, but it can be useful to type and modify text instead of using Vi editor.
 - You are allowed to open as many Firefox tabs as you want in the exam environment but any external resources that are not allowed are typically blocked.
 - Exam questions can be attempted in any order and don't have to be sequential. So be sure to move ahead and come back later.