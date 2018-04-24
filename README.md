# chart-helloworld-with-secrets

* Install `helm-secrets` by running

```bash
helm plugin install https://github.com/futuresimple/helm-secrets
```

* Add the example `projecty.asc` key to pgp by running the following command

```bash
gpg --import pgp/projecty.asc
```

* Export the following Environment variables

```bash
export HELM_BIN=/usr/local/helm
export HELM_PLUGIN_DIR=~/.helm/plugins
```

* Copy `wrapper.sh` from [this repo](https://github.com/futuresimple/helm-secrets) to where `helm` binary is located

```bash
sudo cp wrapper.sh /usr/bin/wrapper.sh
```

* Add your secrets to `secrets.yaml` located in root directory

* Use the secrets like the following

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: helloworld
    labels:
        app: helloworld
        chart: '{{ .Chart.Name }}-{{ .Chart.Version }}'
        release: '{{ .Release.Name }}'
        heritage: '{{ .Release.Service }}'
type: Opaque
data:
  mysecret: {{ .Values.helloworld.secrets.mySecret | b64enc | quote }}
  mysecretTwo: {{ .Values.helloworld.secrets.mySecretTwo | b64enc | quote }}
```

Note that the heirarchy of your secrets will be the same and can be accessed by `.Values.heirarchy`

* Encrypt `secrets.yaml` file by running

```bash
helm secrets enc secrets.yaml
```

* Now you can commit `secrets.yaml` as well

* Run the following command to install the chart using helm-wrapper`

```bash
wrapper.sh helloworld -f secrets.yaml
```