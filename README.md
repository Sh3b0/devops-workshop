# DevOps 3-Day Workshop

End-to-End DevOps project with Python, Docker, GitHub Actions, Ansible, Terraform, AWS, K8s, Helm, Prometheus, Loki, and Grafana.

## Initialization

```bash
python -m venv venv
source venv/bin/activate
pip install flask pytest gunicorn prometheus-flask-exporter
pip freeze > requirements.txt
```

## Testing

```bash
pytest
```

## Docker

To build and push a docker container for the app.

```bash
docker build -t sh3b0/demo .
echo $PASSWORD | docker login -u sh3b0 --password-stdin
docker push sh3b0/demo --all-tags
```

## GitHub Actions

- Action file at: `.github/workflows/action.yml`

- Tests run automatically on each push to the `main` branch that modifies files in the `app` directory.
- Container build and push to DockerHub happens automatically once the tests pass.
  - Make sure to create repo secret for `DOCKER_PASSWORD`

## Terraform

Provision EC2 instance with the necessary configs to the run one instance of the web application.

```bash
nano ~/.aws/config # Update with your AWS connection config
```

```bash
cd terraform
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

## Ansible

Playbook to install Docker and run the app container on the provisioned instance.

```bash
cd ansible
ansible-galaxy collection install community.docker
ansible-playbook main.yml # make sure to update hosts.ini with the outputs from terraform
```

## Kubernetes

Deploy the application to Minikube cluster

```bash
cd k8s
minikube start
kubectl apply -f namespace.yaml -f deployment.yaml -f service.yaml

# Get service URL
minikube service -n ap-ns app-service --url

# Run application as a single docker container with limited resources
docker run --memory="128m" --cpus="0.5" -it -p8080:8080 sh3b0/demo

# Compare both setups with Apache benchmark (with localhost:8080 vs. service URL)
ab -n 5000 <URL>
```

## Monitoring

Run the application with monitoring and visualization stack (Prometheus, Loki, Grafana) with provisioned datasources, dashaboards and alerts. Access grafana at <http://localhost:3000>

```bash
cd monitoring
docker compose up -d
```
