# Poe
Poe
Great — below are complete, step-by-step, runnable solutions for every PoE question in your uploaded file. I reference your PoE task list at the start. Follow each numbered section exactly on your Windows machine (VS Code / Git Bash / PowerShell) — I include the exact commands, file contents to create/edit, expected outputs, and troubleshooting tips.

Reference: your uploaded PoE questions list. 


---

1 — Demonstrate branching in Git (create branch, change, push, merge)

Goal: create feature branch locally, commit changes, push to GitHub, then merge into main.

Pre-reqs: Git installed, GitHub account, a GitHub repo created (or create one named poe-demo).

Steps

1. Open Git Bash (or VS Code terminal). Create a working folder:

mkdir poe-demo
cd poe-demo
git init

Expected: .git folder gets created; git status will show nothing to commit.


2. Create initial file and commit:

echo "Hello PoE" > README.md
git add README.md
git commit -m "Initial commit: add README"
git branch -M main

Expected: commit created (git log --oneline shows one commit).


3. Add your GitHub remote (replace <username> and repo name):

git remote add origin https://github.com/<username>/poe-demo.git
git push -u origin main

If using SSH: git remote add origin git@github.com:<username>/poe-demo.git


4. Create and switch to new branch feature:

git checkout -b feature

Now make a change:

echo "New feature: version 1" >> README.md
git add README.md
git commit -m "Add feature: version 1"


5. Push the branch to GitHub:

git push -u origin feature

Expected on GitHub: feature branch appears.


6. Merge feature into main locally:

git checkout main
git merge feature

If merge is fast-forward you’ll see message; otherwise merge commit.


7. Push merged main to GitHub:

git push origin main



Notes / Troubleshooting

If git push fails due to authentication, configure GitHub credentials or SSH keys.

If GitHub default branch is main vs master, ensure you use correct name (git branch -M main sets it).



---

2 — Demonstrate git reset --hard vs git reset --soft

Goal: show how --soft keeps changes staged and --hard discards them.

Steps

1. Start in a fresh repo:

mkdir reset-demo && cd reset-demo
git init
echo "Line 1" > file.txt
git add file.txt
git commit -m "commit1"


2. Make more commits:

echo "Line 2" >> file.txt
git commit -am "commit2"

echo "Line 3" >> file.txt
git commit -am "commit3"


3. Inspect history:

git log --oneline --graph --decorate
# Shows commit3 (HEAD), commit2, commit1


4. Soft reset by 1 commit:

git reset --soft HEAD~1

Effects:

HEAD moves back one commit (to commit2).

Changes from commit3 are left in the staging area (git status shows staged changes).

You can git commit -m "recommit or modify" to commit again.



5. Hard reset by 1 commit: (Be careful; this discards)

git reset --hard HEAD~1

Effects:

HEAD moves back one commit.

Working tree and index reset — changes from the removed commit are gone.




Demonstration to push to remote

If you previously pushed commits and then do a local --hard reset and want remote to match, you must force-push (only if acceptable):

git push origin main --force

Warning: --force rewrites remote history — use carefully.


Notes

--soft: keep changes staged — safe for reworking commits.

--hard: irrevocably delete uncommitted changes (unless you recover via reflog).



---

3 — GitHub → GitLab cross-platform repository management

Goal: clone from GitHub locally and push the same code to a new GitLab repository.

Steps

1. On GitHub: ensure repo exists (e.g., poe-demo), or create it.


2. Clone GitHub repo locally:

git clone https://github.com/<username>/poe-demo.git
cd poe-demo


3. Create a new empty project on GitLab via browser (e.g., poe-demo-gitlab). Copy its remote URL.


4. Add GitLab as a new remote and push:

git remote add gitlab https://gitlab.com/<username>/poe-demo-gitlab.git
git push -u gitlab main

Or push all branches:

git push --all gitlab
git push --tags gitlab


5. Make a change locally, commit and push to both:

echo "Added README note for GitLab" >> README.md
git commit -am "Note for GitLab"
git push origin main
git push gitlab main



Notes

You can keep both remotes (origin for GitHub, gitlab for GitLab).

For CI/CD on GitLab, push triggers pipelines if configured.



---

4 — GitLab → Bitbucket integration

Goal: clone from GitLab and push to Bitbucket.

Steps

1. On GitLab: have a repository (e.g., poe-demo-gitlab). Clone locally:

git clone https://gitlab.com/<username>/poe-demo-gitlab.git
cd poe-demo-gitlab


2. Create an empty repository on Bitbucket (via browser). Copy its remote URL.


3. Add Bitbucket remote and push:

git remote add bitbucket https://<username>@bitbucket.org/<username>/poe-demo-bitbucket.git
git push -u bitbucket main
git push --all bitbucket
git push --tags bitbucket


4. Verify on Bitbucket web UI that code exists.



Notes

If using HTTP remotes, Bitbucket may prompt for credentials; consider using app passwords or SSH keys.

git remote -v lists all configured remotes.



---

5 — Merge conflict demonstration and resolution

Goal: create two branches with conflicting changes, attempt merge, resolve conflict manually.

Steps (simple reproducible flow)

1. Start repo and initial file:

mkdir conflict-demo && cd conflict-demo
git init
echo "Line A" > conflict.txt
git add conflict.txt
git commit -m "Initial commit"


2. Create branch branchA and change line:

git checkout -b branchA
echo "Change from A" > conflict.txt
git commit -am "branchA change"


3. Switch to main and create branchB with a conflicting change:

git checkout main
git checkout -b branchB
echo "Change from B" > conflict.txt
git commit -am "branchB change"


4. Merge branchA into main (first bring main up):

git checkout main
git merge branchA   # likely fast-forward if no other changes


5. Now merge branchB into main — this should cause a conflict:

git merge branchB

Git will report conflict in conflict.txt:

CONFLICT (content): Merge conflict in conflict.txt
Automatic merge failed; fix conflicts and then commit the result.


6. Resolve conflict:

Open conflict.txt in VS Code. You’ll see markers like:

<<<<<<< HEAD
Change from A
=======
Change from B
>>>>>>> branchB

Edit the file to the desired final content, e.g.:

Final: combined from A and B

Save file, then:

git add conflict.txt
git commit -m "Resolve merge conflict between branchA and branchB"



7. Push to remote if desired:

git push origin main



Tips

Use git status to see conflicting files.

VS Code has a GUI conflict resolver showing "Accept Incoming / Accept Current / Accept Both".



---

6 — Basic Jenkins pipeline for website updates

Goal: create a Jenkins pipeline (Jenkinsfile in repo) that builds and deploys a simple static website.

Where to run: Jenkins installed on local machine (http://localhost:8080), or a server.

Steps

1. Install Jenkins (download and run), then open http://localhost:8080 and complete setup.


2. In your website repo create Jenkinsfile at repo root with this content:

pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        echo 'Building static site (no-op for static HTML)'
        // e.g., if using static site generator, run build commands
      }
    }
    stage('Test') {
      steps {
        echo 'No tests for static site'
      }
    }
    stage('Deploy') {
      steps {
        // for demo, copy files to a local folder or print message
        sh 'mkdir -p /tmp/poe-website || true'
        sh 'cp -r * /tmp/poe-website'
        echo 'Deployed to /tmp/poe-website'
      }
    }
  }
}

On Windows, replace sh with bat steps, or run via WSL.



3. Create a new Pipeline job in Jenkins:

New Item → Pipeline → set SCM to Git and give repo URL.

Jenkins will read Jenkinsfile and run pipeline when you click Build Now.



4. Triggering on push:

Set up webhook on GitHub → Jenkins (or use GitHub plugin / GitLab plugin) so pushes trigger build.




Notes

For a real deployment you'd replace the Deploy stage with an SCP/rsync to a web server or a container deployment.



---

7 — Containerized Jenkins pipeline (Jenkins runs build inside container)

Goal: use Docker to run pipeline steps inside a container (example Node static build).

Pre-reqs: Jenkins configured with Docker installed on Jenkins host and proper permissions (Docker plugin or agent with Docker available).

Jenkinsfile example

pipeline {
  agent {
    docker { image 'node:18' }
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Install') {
      steps {
        sh 'npm install'     // runs inside node:18 container
      }
    }
    stage('Build') {
      steps {
        sh 'npm run build'   // if your project has build script
      }
    }
    stage('Archive') {
      steps {
        archiveArtifacts artifacts: 'dist/**', fingerprint: true
      }
    }
  }
}

How to run

1. Save Jenkinsfile to repo.


2. Jenkins job configured to use SCM repo.


3. Ensure Jenkins user can communicate with Docker daemon (on Linux often add jenkins user to docker group; on Windows use Docker Desktop).



Notes

For Windows host, use Docker Desktop and configure Jenkins accordingly.

If Docker-in-Docker is needed, more setup required (DinD privileged containers).



---

8 — Build and publish a custom Docker httpd image

Goal: create Docker image based on httpd, add custom website, test locally, push to Docker Hub.

Pre-reqs: Docker Desktop installed, DockerHub account.

Project layout

docker-httpd/
  ├─ website/
  │   └─ index.html
  └─ Dockerfile

Steps

1. Create website/index.html:

<!doctype html>
<html><body><h1>Hello from custom httpd image</h1></body></html>


2. Create Dockerfile:

FROM httpd:2.4
COPY ./website/ /usr/local/apache2/htdocs/


3. Build image:

cd docker-httpd
docker build -t myhttpd:1.0 .


4. Run locally:

docker run -d --name myhttpd -p 8080:80 myhttpd:1.0

Open http://localhost:8080 to see page.


5. Tag and push to Docker Hub:

docker login     # provide Docker Hub credentials
docker tag myhttpd:1.0 <dockerhub-username>/myhttpd:1.0
docker push <dockerhub-username>/myhttpd:1.0


6. To pull on another machine:

docker pull <dockerhub-username>/myhttpd:1.0
docker run -d -p 8080:80 <dockerhub-username>/myhttpd:1.0



Troubleshooting

If docker build fails: check Docker daemon running.

If port 8080 is in use, choose another host port mapping like -p 9090:80.



---

9 — Maven-based calculator application with JUnit tests

Goal: create Java project via Maven, write Calculator class, add JUnit tests, run tests, package jar.

Pre-reqs: Java JDK installed (11+ recommended), Maven installed.

Steps

1. Generate project:

mvn archetype:generate -DgroupId=com.poe -DartifactId=calculator -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd calculator

This creates standard Maven layout: src/main/java, src/test/java.


2. Create Calculator.java at src/main/java/com/poe/Calculator.java:

package com.poe;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    public int subtract(int a, int b) {
        return a - b;
    }
    public int multiply(int a, int b) {
        return a * b;
    }
    public int divide(int a, int b) {
        if (b == 0) throw new IllegalArgumentException("Divide by zero");
        return a / b;
    }
    public static void main(String[] args) {
        Calculator c = new Calculator();
        System.out.println("2+3 = " + c.add(2,3));
    }
}


3. Write JUnit tests CalculatorTest.java at src/test/java/com/poe/CalculatorTest.java:

package com.poe;

import org.junit.Test;
import static org.junit.Assert.*;

public class CalculatorTest {
    @Test
    public void testAdd() {
        Calculator c = new Calculator();
        assertEquals(5, c.add(2, 3));
    }
    @Test
    public void testSubtract() {
        Calculator c = new Calculator();
        assertEquals(1, c.subtract(3, 2));
    }
    @Test(expected = IllegalArgumentException.class)
    public void testDivideByZero() {
        Calculator c = new Calculator();
        c.divide(1, 0);
    }
}

> The quickstart archetype may use JUnit 4 by default. If you prefer JUnit 5, update pom.xml dependencies accordingly.




4. Run tests:

mvn test

Expected: tests run and pass.


5. Package jar:

mvn package

Result: target/calculator-1.0-SNAPSHOT.jar created.


6. Run jar (if you want to run main):

java -cp target/calculator-1.0-SNAPSHOT.jar com.poe.Calculator



Notes

If tests fail, check stack traces printed by Maven.

To create an executable jar, configure Maven maven-jar-plugin or maven-shade-plugin in pom.xml.



---

10 — Kubernetes with Minikube (deploy simple Docker container)

Goal: run Minikube locally, deploy an httpd container as a Deployment and expose it.

Pre-reqs: Docker Desktop, Minikube installed, kubectl installed.

Steps

1. Start Minikube:

minikube start

Expected: minikube cluster starts (driver uses Docker Desktop).


2. Create a Deployment using Docker Hub httpd image:

kubectl create deployment myweb --image=httpd:2.4


3. Expose the deployment:

kubectl expose deployment myweb --type=NodePort --port=80


4. Check status:

kubectl get pods
kubectl get svc

kubectl get svc shows myweb service with a NodePort.


5. Open the service in browser:

minikube service myweb

This opens the service URL in your browser.


6. Scale the deployment:

kubectl scale deployment myweb --replicas=3
kubectl get pods


7. Update the image (rolling update):

kubectl set image deployment/myweb httpd=httpd:2.4
kubectl rollout status deployment/myweb


8. Cleanup:

kubectl delete service myweb
kubectl delete deployment myweb
minikube stop



If you want to deploy your custom Docker image

Build image and push to DockerHub, or use Minikube’s Docker daemon:

eval $(minikube -p minikube docker-env)   # in Git Bash / bash
docker build -t myhttpd:1.0 .
kubectl create deployment mycustom --image=myhttpd:1.0

(This builds image inside Minikube's Docker so no push required.)


Troubleshooting

If minikube start fails, check virtualization settings (enable Hyper-V or WSL2 backend) and Docker daemon running.

If minikube service doesn't open, fetch NodePort and open http://<minikube-ip>:<nodePort> manually:

minikube ip
kubectl get svc myweb -o=jsonpath='{.spec.ports[0].nodePort}'



---

Final notes & next steps

I followed the tasks exactly from your uploaded PoE file. 

If you want, I can:

Produce a single print-ready PDF with the steps and screenshots placeholders.

Generate ready-to-run shell scripts or Jenkinsfile files for each relevant task.

Provide a video-lessons style checklist you can tick off in the lab.



Which of those would help you most next?
