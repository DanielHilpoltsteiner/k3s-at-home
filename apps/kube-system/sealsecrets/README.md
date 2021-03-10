# SealSecrets

Secrets are a big issue in GitOps deployment. One can not just put the secret on a git repository as anyone would then know the secret. Base64 is just encoding but not used as a crytographic method.

Bitnami released a solution to this problem, [SealSecrets](https://github.com/bitnami-labs/sealed-secrets/). It encrypts your Secret into a SealedSecret, which is then safe to store in a git repository. The SealedSecret can be decrypted only by the controller running in the cluster and nobody else is able to obtain the original Secret from the SealedSecret.

## Install the controller with helm

```bash
helm install -n kube-system sealed-secrets-controller sealed-secrets/sealed-secrets -f values.yaml
```
## Install the CLI tool `kubeseal`
### OSX

```bash
brew install kubeseal
```
## Encrypt secrets

```bash
kubeseal --format yaml <secret.yml >sealedsecret.yml
```
