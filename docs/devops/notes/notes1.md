
Jeśli korzystamy z GCP Artifact Registry, to trzeba stworzyć konto serwisowe, pobrać dane uwierzytelniające w formacie JSON, i dodać je jako sekret repozytorium, by móc pushować zbudowane obrazy.


1. [Workflow for testing Java service](files/build-test-java-project.yaml)
2. [Workflow for building/deploying Java service](files/build-java-deploy-gcp.yaml)