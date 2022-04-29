## Install Terraform
```
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

## Update version

```
brew update
brew upgrade hashicorp/tap/terraform
```

## Verify installation

```
terraform -help
terraform -help plan
```

## Enable tab completion

```
touch ~/.zshrc
terraform -install-autocomplete
```