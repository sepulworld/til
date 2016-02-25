# Use Null Resource to Run Remote Exec Commands on Multiple Desperate Resources  
The [null_resource](https://www.terraform.io/docs/provisioners/null_resource.html) in this example is used to run a remote-exec command to restart a service on a cluster of systems once a different system has finished provisioning.

```terraform
resource "aws_instance" "name_node" {
# system terraform variables here...
}

resource "null_resource" "restart_clientnode_services" {
  # trigger this resouce upon 'name_node' instance finishing 
  triggers {
    cluster_instance_ids = "${aws_instance.name_node.id}"
  }

  #Run accross all clientnode systems, built previously via count perhaps
  connection {
    host = "${element(aws_instance.clientnode.*.public_ip}"
  }
  
  provisioner "remote-exec" {
    # Run bootstrap on each node in the clutser
    inline = [
      "bootstrap-cluster.sh"
    ]
  }
}

```
