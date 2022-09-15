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

<img width="1166" alt="image" src="https://user-images.githubusercontent.com/49791498/190294042-9864f355-2746-4067-a07a-5a8d8a2b3fa7.png">

<img width="1166" alt="image" src="https://user-images.githubusercontent.com/49791498/190295226-a5046fa0-d08c-45be-9440-5ccf5bcdd2af.png">

*Accessed via ELB's URL*