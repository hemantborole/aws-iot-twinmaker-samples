# Setup Local Grafana for an AWS IoT TwinMaker Workspace

We will first walk through the setup of Grafana in a dev environment running in a Docker container on the developer's local desktop. Once Grafana is running, the configuration for running on a local container vs a cloud instance are similar.

## Create a Docker Container for your Workspace

For the CookieFactory sample this can be achieved by running the setup_local_grafana_docker.sh script from the [src/modules/grafana](../src/modules/grafana) directory. This will create a local container with a recent Grafana image that has the required AWS IoT TwinMaker extensions added to it.

### Cloud9

```bash
sh $GETTING_STARTED_DIR/src/modules/grafana/setup_cloud9_grafana_docker.sh
```

### Local

```bash
sh $GETTING_STARTED_DIR/src/modules/grafana/setup_local_grafana_docker.sh
```

## Browse to your Grafana instance

### Cloud9

If you are running in Cloud9 you can see your Grafana instance by opening your instance's public hostname in your browser. You can run the following from the bash terminal in Cloud9 to print out your instance's public hostname

```
TOKEN=`curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && EC2_HOSTNAME=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) && echo "http://${EC2_HOSTNAME}"
```

If the page is taking a long time to load, it may be that the Ingress rules for your EC2 instance are not matching the IP address from EC2. To debug, you can try temporarily changing the Ingress rule for HTTP in your security group to "Anywhere-IPv4"

### Local

If you ran the `setup_local_grafana_docker.sh` script from above you can see your Grafana instance at http://localhost:3000

## Login to Grafana

Once you load the grafana page, you can then logon as Administrator (default username: `admin` password: `admin`). You will be asked to change the default password on first run.

![Grafana Login](images/grafana_login.png)

## Configure the AWS IoT TwinMaker Datasource

The next step is to configure Grafana to be able to connect to your IoT TwinMaker Workspace through a data source plugin.

- From the Grafana home page, select the configuration (gear icon) on the left nav bar and then select 'Data sources'
- Select the 'Add data source' button and pick the 'AWS IoT TwinMaker Datasource'.
- You can then configure the plugin by filling in your credential settings and select your workspace.
- Click the "Save and Test" button to verify your data source has been configured correctly.
- (Optional but recommended) To restrict AWS permissions to be ReadOnly on the IoT TwinMaker workspace of your datasource: enter the IAM role ARN of the dashboard role you created after you created the IoT TwinMaker workspace.

Note: the docker setup creates a mapping to your `~/.aws/credentials` file so by using `Authentication Provider: "AWS SDK Default" or "Credentials file"` you can use profiles configured in your environment (or default profile if nothing specified). If running in Cloud9, you can use `AWS SDK Default`.

(Optional) Endpoints for AWS IoT TwinMaker:

Note: if no endpoint is provided, it will default to the `us-east-1` endpoint

- us-east-1: https://iottwinmaker.us-east-1.amazonaws.com

---

## Troubleshooting

### AWS IoT TwinMaker Datasource does not appear in list

If it does not show up in your list you may need to refresh the page a few times.

### User: arn:aws:sts::[accountId]:assumed-role/iottwinmaker_development_role/[instance_id] is not authorized to perform: sts:AssumeRole on resource: ...Dashboard IAM role...

You will need to edit your Dashboard IAM role to grant permissions to grant assume role permissions to the instance. For quick troubleshooting, you can also temporarily leave the `Assume Role ARN` field blank to default to your SDK credentials. (not recommended for production use-cases)

---

## License

This project is licensed under the Apache-2.0 License.