# intro-to-CloudFormation
Foundational knowledge you need to start working with AWS CloudFormation

### CloudFormation Commands
- To create a stack:
```bash
aws cloudformation create-stack \
--stack-name <value> \
--template-body file://file.yml \
--parameters file://file.json \
```

- To update an existing stack
```bash
aws cloudformation update-stack \
--stack-name <value> \
--template-body file://file.yml \
--parameters file://file.json \
```

- To delete a stack
```bash
delete-stack \
--stack-name <value>
```

You can find more CloudFormation commands [here](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html).

**Note**: Deleting a stack, deletes all resources which the stack provisioned.
