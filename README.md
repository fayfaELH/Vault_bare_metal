# Add the repo
helm repo add hashicorp https://helm.releases.hashicorp.com

# Download the vault default values template
# The vault UI isn't enabled, therefore it must enable it
curl https://raw.githubusercontent.com/hashicorp/vault-helm/main/values.yaml > vault-values.yaml

# Install Vault
helm install \
  vault hashicorp/vault \
  -f vault-values.yaml \
  --namespace vault \
  --create-namespace

# vault-0 does not get ready. You can check by get pod command.
# Please refer to this url: https://dwops.com/blog/deploy-hashicorp-vault-on-kubernetes-using-helm/
kubectl get pod -n vault

# The logs give the following
# 2021-08-28T08:16:27.987Z [INFO]  proxy environment: http_proxy="" https_proxy="" no_proxy="" 
# 2021-08-28T08:16:30.885Z [INFO]  core: security barrier not initialized 
# 2021-08-28T08:16:30.885Z [INFO]  core: seal configuration missing, not initialized 
# 2021-08-28T08:16:35.785Z [INFO]  core: security barrier not initialized
#
# ..........
kubectl logs -f vault-0 -n vault

# To unseal the vault, we need to initialise the vault operator that will provide some Unseal keys that is going to be used to unseal the vault. Save the Unseal keys and Initial Root Token in a file for later use.
# Unseal Key 1: fBgeKlqc5yMhPdof/YGYcY5ZOa9kKvhw7lFGh/NSvfpS
# Unseal Key 2: jAvGUI5rrsDIlHd26KluJ2JU0hQX5Ia7EzMbxv/Jj6av
# Unseal Key 3: 1JQv0L1yhl9cERcUMA0Magz4sCJ/lPBHseeRJFP20A2L
# Unseal Key 4: tCBrx9dEnUrtFz2cfNO/AsGhENGLTDM4ugy1bqZe1JoL
# Unseal Key 5: DW2tCWDdHRyNXbN6DulcQpKuhEMG5acYSkOU3G2pkCr9
#
# Initial Root Token: s.Yz4mBj1oSVkjKpF3HgZhLoXY
#
# ............
kubectl exec -ti vault-0 -n vault -- vault operator init

# Unseal the vault using the keys shared above until the threshold is met:
# kubectl exec -ti vault-0 -n vault -- vault operator unseal
# kubectl exec -ti vault-0 -n vault -- vault operator unseal
kubectl exec -ti vault-0 -n vault -- vault operator unseal
