# Use Null Resource to Run Remote Exec Commands on Multiple Desperate Resources  
The [null_resource](https://www.terraform.io/docs/provisioners/null_resource.html) in this example is used to run a remote-exec command to restart a service on a cluster of client systems once a seperate primary system has finished provisioning.

```terraform
resource "aws_instance" "primary_node" {
# system terraform variables here...
}

resource "aws_instance" "clientnode" {
  count = "${var.clientnode_count}"
  ami   = "${var.clientnode_ami}"
  private_ip = "${lookup(var.clientnode_instance_ips, count.index)}"
  ...
}


resource "null_resource" "restart_clientnode_services" {
  count = "${var.clientnode_count}"

  # trigger this resouce upon 'primary_node' instance finishing 
  triggers {
    cluster_instance_ids = "${aws_instance.primary_node.id}"
  }

  #Run accross all clientnode systems, built previously via count perhaps
  connection {
    host = "${element(aws_instance.clientnode.*.private_ip, count.index)}"
  }
  
  provisioner "remote-exec" {
    # Run service restart on each node in the clutser
    inline = [
      "sudo service example-client restart"
    ]
  }
}

```
