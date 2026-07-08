# ichika-cd-secrets
Ichika K3s CD/CI secrets managed with SOPS

## age

### Create & import age key
```bash
# Generate an age key with age using age-keygen
age-keygen -o ~/sops/age/keys.txt

# Create a secret with the age private key
cat ~/sops/age/keys.txt |
kubectl create secret generic sops-age \
  --namespace=flux-system \
  --from-file=age.agekey=/dev/stdin
```

## SOPS

Place key file in SOPS default lookup directory:
```bash
$XDG_CONFIG_HOME/sops/age/keys.txt
```

Set environment variables:
```bash
# age recipients
export SOPS_AGE_RECIPIENTS="age_recipients"

# age key
export SOPS_AGE_KEY_FILE="/home/user/keys.txt" # Provide full path (e.g. /home/user/keys.txt)
# Or
export SOPS_AGE_KEY="age_key"
```

### Encrypt
```bash
export AGE_PUBLIC_KEY="age_public_key" # May omit this if SOPS_AGE_RECIPIENTS is set

sops encrypt \
  --age="$AGE_PUBLIC_KEY" \ # May omit this if SOPS_AGE_RECIPIENTS is set
  --encrypted-regex '^(data|stringData)$' \
  --in-place file.yaml
```

### Edit
```bash
export SOPS_EDITOR="vi" # Or an editor of your choice

sops edit file.yaml
```

### Decrypt
```bash
# Should have one of SOPS_AGE_KEY_FILE, SOPS_AGE_KEY, SOPS_AGE_KEY_CMD configured
sops decrypt --in-place file.yaml

# Or
sops -d -i file.yaml
```