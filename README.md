Create your backstage app

```shell
echo backstage | npx @backstage/create-app
```

```
cd backstage
cp -R 


Installation backstage

Allez dans values-openshift.yaml et dans la partie ```ingress``` remplacer le path dans host par l'url de votre propre cluster

puis
```
oc new-project backstage
helm install backstage backstage/backstage -f values-openshift.yaml
```
