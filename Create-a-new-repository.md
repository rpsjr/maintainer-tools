To initialize the branches on the Github repository, locally run the following commands:

    export REPO=name_of_the_repo
    mkdir $REPO
    cd $REPO
    git init

Then, for each branch run:

    export BRANCH=8.0
    git checkout -b $BRANCH

Create README, .travis.yml, .gitignore... using https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files as a starting point

    git add . 
    git commit
    git remote add origin git@github.com:OCA/$REPO
    git push origin $BRANCH:$BRANCH
