# crate-ci Examples

These examples each show case a specific best practice from the crate-ci docs.

## Contributing

### Cloning this repo

After cloning:

1. Run `git submodule init`

### Pulling changes from this repo:

After pulling:

1. Run `git submodule update`

### New examples

1. Create an example in your repo
2. Request it be transfered to the `crate-ci` org.  If this example is for a
   hosting service that `crate-ci` is not on yet, please request us to create
   an org on it first.
3. Run `git submodule add REPO`
4. Create a PR with the new submodule added.

### Updating reference to examples

1. Run `git submodule foreach git pull`
