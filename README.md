# Backstage

## Install backstage

This repo contains the material to deploy a janus on openshift.

```shell
oc apply -f gitops/ns.yaml
oc apply -f gitops/sub.yaml
oc apply -f gitops/ClusterRoleBinding.yaml 
```


Dans le fichier ```gitops/base/backstage/backstage-app-config.yaml``` remplace ```dashboardUrl``` et ```baseUrl``` avec l'ingres correct pour votre domaine

mettez a jour le repo

```shell
git add --all
git commit -m "update path"
git push
```

Create the argoCD chain project

```shell
oc apply -f gitops/argocd/project.yaml
```

Create the argoCD Application

```shell
oc apply -f gitops/argocd/application.yaml
```

After the creation you need to update the psql database using the following command

```shell
oc rsh postgres-78b8db49c9-k9trn

psql

ALTER USER feven WITH CREATEDB;
```



## Configure integration with argocd

```shell
oc patch argocd openshift-gitops -n openshift-gitops --type merge --patch '{"spec":{"extraConfig":{"accounts.admin": "apiKey"}}}'
```

Then go in your argocd application login and go in Settings>Account>admin and click on Generate New. Keep the generated token, he will be create in the secret on next step

## Configure integration with github

Go in ```https://github.com/settings/developers``` click in ```New Oauth App``` and complete the form. In Homepage URL put the URL of the backstage route (ex: https://red-hat-developer-hub-backstage.apps.rosa-neve.kset.p1.openshiftapps) and for Authorization callback URL put the backstage route and add /api/auth/github/handler/frame (ex https://backstage-developer-hub-backstage-developer-hub.apps.rosa-neve.kset.p1.openshiftapps.com//api/auth/github/handler/frame). Then create your Oauth app and click on ```Generate a new client secret```. Keep the clien ID and the Client Secret.   

Finally go in ```https://github.com/settings/tokens``` and Generate a New Token. Give access to repo and workflow and click on Generate token. Keep the value as GH_TOKEN.

## Generate backstage secret 

```
oc create secret generic backstage-secret -n backstage --from-literal=GH_TOKEN=<GH_TOKEN> --from-literal=GH_CLIENT_ID=<GH_CLIENT_ID> --from-literal=GH_CLIENT_SECRET=<GH_CLIENT_SECRET> --from-literal=ARGO_API_TOKEN=<ARGO_API_TOKEN>
```
