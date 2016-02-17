# View Spot Instance Pricing History 

The following aws cli command will return an array of JSON results

```bash
#See price changes from Feb 9th 2016 to Feb 15th 2016
aws ec2 describe-spot-price-history --product-description "Linux/UNIX (Amazon VPC)" --instance-types m3.xlarge --start-time 2016-02-09T07:08:09 --end-time 2016-02-15T08:09:10
```

Example output snippet
```bash
{
   [
        {
            "Timestamp": "2016-02-09T11:26:23.000Z",
            "ProductDescription": "Linux/UNIX",
            "InstanceType": "m3.xlarge",
            "SpotPrice": "0.041800",
            "AvailabilityZone": "us-west-2a"
        },
        {
            "Timestamp": "2016-02-09T11:24:59.000Z",
            "ProductDescription": "Linux/UNIX",
            "InstanceType": "m3.xlarge",
            "SpotPrice": "0.039100",
            "AvailabilityZone": "us-west-2b"
        }
     ...
   ]
}

results could be useful if you want to vary your bid price based upon some trends in the pricing history. 

For fancy graphs head over to the AWS console under the EC2 heading, then Spot Instances.  Click on the "Pricing History" 
