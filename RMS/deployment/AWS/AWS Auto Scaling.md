# AWS Auto Scaling #

Auto Scaling helps maintain application availability and allows to scale our Amazon EC2 capacity up or down automatically according to conditions we define. We can use Auto Scaling to help ensure that we are running our desired number of Amazon EC2 instances. Auto Scaling can also automatically increase the number of Amazon EC2 instances during demand spikes to maintain performance and decrease capacity during lulls to reduce costs. 

For example, the following Auto Scaling group has a minimum size of 1 instance, a desired capacity of 2 instances, and a maximum size of 4 instances. The scaling policies that we define adjust the number of instances, within the minimum and maximum number of instances, based on the criteria that we specify.

![as-basic-diagram.png](https://bitbucket.org/repo/dBgzdj/images/2338159072-as-basic-diagram.png)


# Auto Scaling Setup

### Step 1: Create a Launch Configuration
A launch configuration specifies the type of EC2 instance that Auto Scaling creates for us. We create the launch configuration by including information such as the Amazon Machine Image (AMI) ID to use for launching the EC2 instance, the instance type, key pairs, security groups, and block device mappings, among other configuration settings.

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2. On the navigation bar, select a region. The Auto Scaling resources that we create are tied to the region we specify and are not replicated across regions. For more information, see [Example: Distributing Instances Across Availability Zones.](https://docs.aws.amazon.com/autoscaling/latest/userguide/auto-scaling-benefits.html#arch-AutoScalingMultiAZ)
3. On the navigation pane, under Auto Scaling, choose Launch Configurations.
4. On the Welcome to Auto Scaling page, choose Create Auto Scaling group.
5. On the Create Auto Scaling Group page, choose Create launch configuration.
6. On the Choose AMI page, there is a list of basic configurations, called Amazon Machine Images (AMIs), that serve as templates for our instance. Select the 64-bit Amazon Linux AMI.
7. On the Choose Instance Type page, select a hardware configuration for our instance. It is recommended to keep the default, a t2.micro instance. Choose **Next: Configure details.** 
	* **Note**: T2 instances must be launched into a subnet of a VPC. If you select a t2.micro instance but don't have a VPC, one is created for you. This VPC includes a public subnet in each Availability Zone in the region.
8. On the Configure Details page, do the following:
    - For Name, type a name for the launch configuration (for example, my-first-lc).
    - For Advanced Details, select an IP address type. If you want to connect to an instance in a VPC, you must select an option that assigns a public IP address. If you want to connect to your instance but aren't sure whether you have a default VPC, select Assign a public IP address to every instance.
    - Choose Skip to review.
9. On the Review page, choose Edit security groups. Follow the instructions to choose an existing security group, and then choose Review.
10. On the Review page, choose Create launch configuration.
11. On the Select an existing key pair or create a new key pair page, select one of the listed options. Note that you won't connect to your instance as part of this tutorial. Therefore, you can select Proceed without a key pair unless you intend to connect to your instance.
12. Choose Create launch configuration.

### Step 2: Create an Auto Scaling Group

An Auto Scaling group is a collection of EC2 instances, and the core of the Auto Scaling service. We create an Auto Scaling group by specifying the launch configuration we want to use for launching the instances and the number of instances our group must maintain at all times. We also specify the Availability Zone in which we want the instances to be launched.

1. On the Configure Auto Scaling group details page, do the following:
   * For Group name, type a name for your Auto Scaling group (for example, my-first-asg.
   * Keep Group size set to the default value of 1 instance for this tutorial.
   * If you are launching a t2.micro instance, you must select a VPC in Network. Otherwise, if your account supports EC2-Classic and you are launching a type of instance that doesn't require a VPC, you can select either Launch into EC2-Classic or a VPC.
   * If you selected a VPC in the previous step, select one or more subnets from Subnet. If you selected EC2-Classic in the previous step, select one or more Availability Zones from Availability Zone(s).
   * Choose Next: Configure scaling policies.
2. On the Configure scaling policies page, select Keep this group at its initial size and choose Review.
3. On the Review page, choose Create Auto Scaling group.
4. On the Auto Scaling group creation status page, choose Close.

### Step 3: Verify Your Auto Scaling Group ###
Now that we have created our Auto Scaling group, we are ready to verify that the group has launched an EC2 instance.

1. On the Auto Scaling Groups page, select the Auto Scaling group that you just created.
2. The Details tab provides information about the Auto Scaling group.
3. On the Activity History tab, the Status column shows the current status of your instance. While your instance is launching, the status column shows In progress. The status changes to Successful after the instance is launched. You can also use the refresh button to see the current status of your instance.
4. On the Instances tab, the Lifecycle column shows the state of your instance. You can see that your Auto Scaling group has launched your EC2 instance, and that it is in the InService lifecycle state. The Health Status column shows the result of the EC2 instance health check on your instance.
5. (Optional) If you want, you can try the following experiment to learn more about Auto Scaling. The minimum size for your Auto Scaling group is 1 instance. Therefore, if you terminate the running instance, Auto Scaling must launch a new instance to replace it.
    * On the Instances tab, select the ID of the instance. This shows you the instance on the Instances page.
    * Choose Actions, Instance State, Terminate. When prompted for confirmation, choose Yes, Terminate.
    * On the navigation pane, choose Auto Scaling Groups, Activity History. The default cooldown for the Auto Scaling group is 300 seconds (5 minutes), so it takes about 5 minutes until you see the scaling activity. When the scaling activity starts, you'll see an entry for the termination of the first instance and an entry for the launch of a new instance. The Instances tab shows the new instance only.
    * On the navigation pane, choose Instances. This page shows both the terminated instance and the running instance.


# Current Auto Scaling Config #
Currently there is one Auto Scaling group. Its condition to increase or decrease size is as follows:

### Auto Scaling Increase ###
If average CPU Utilization is greater than 80% for a period of 5 minutes, it will increase one instance. 

### Auto Scaling Decrease ###
If average CPU Utilization is less than 50% for a period of 5 minutes, it will decrease one instance. 

# References #

1. [Getting Started with Auto Scaling](http://docs.aws.amazon.com/autoscaling/latest/userguide/GettingStartedTutorial.html)
