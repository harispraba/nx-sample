# Monorepo with Nx

This is a Monorepo app created using Nx (nx.dev)

# What's Inside:
- Two backend project (`node-api` and `go-api`)
- Both project included with `Dockerfile`
- Basic Nx command to `build`, `test`, `run` and `docker-build`
- `Jenkinsfile` to integrate with Jenkins pipeline
- Basic Kubernetes manifest

# Interacting with Nx projects
### Start the application
Run `npx nx serve node-api` or `npx nx serve go-api` to start the development server. Happy coding!

### Running tasks
To execute tasks with Nx use the following syntax:

```
npx nx <target> <project> <...options>
```
You can also run multiple targets:
```
npx nx run-many -t <target1> <target2>
```
..or add -p to filter specific projects

```
npx nx run-many -t <target1> <target2> -p <proj1> <proj2>
```

# How to run the Jenkins CI/CD
1. Make sure to run Jenkins, you can use this Jenkins IAC sample repo https://github.com/harispraba/jenkins-iac
   - Jenkins will run as docker container so Docker needs to be installed on the host
2. Add needed credential to Jenkins
   - `artifactRegistryAuth` credential kind ⇒ Username with Password
   - `kubeconfig-file` credential kind ⇒ Secret file
   - `gke-service-account` credential kind ⇒ Secret file
3. Modify `Jenkinsfile` to your needs. Replace value in registry url and registry auth url.