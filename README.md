# k8s_cluster_guide
=======================
https://github.com/Krreesh/kops/blob/master/docs/getting_started/aws.md
======================
<pre>
#update apt-get
sudo apt-get update
sudo apt-get install -y awscli
#export AWS credentials variables
export AWS_ACCESS_KEY_ID=XXXXXMU5
export AWS_SECRET_ACCESS_KEY=XXXXQzt

#create route53 hosted zone for subdomain (k8s.bang.tk)
#copy NS records of that subdomain. In parent hosted zone (bang.tk), create subdomain (k8.bang.tk) NS record with NS server names copied from subdomain:
{
  "Comment": "Create a subdomain NS record in the parent domain",
  "Changes": [
    {
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "k8s.bang.tk",
        "Type": "NS",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "ns-2003.awsdns-58.co.uk"
          },
          {
            "Value": "ns-154.awsdns-19.com"
          },
          {
            "Value": "ns-1515.awsdns-61.org"
          },
          {
            "Value": "ns-887.awsdns-46.net"
          }
        ]
      }
    }
  ]
}

aws route53 change-resource-record-sets \
 --hosted-zone-id Z08734811BR2FWGBVJ0VB \
 --change-batch file://subdomain.json

#installing kops
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | 

chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
#install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

#export cluster name and state store name
export NAME=bang.tk
export KOPS_STATE_STORE=s3://kluster.cluster.bang.tk

#PUBLIC DOMAIN if export NAME=k8s.bang.tk
kops create cluster $NAME --zones us-east-1a --node-size t2.micro --master-size t2.large --master-volume-size 20 --node-volume-size 8 --dns-zone=$NAME --dns public
#PRIVATE DOMAIN if export NAME=krreesh.local
kops create cluster $NAME --zones us-east-1a --node-size t2.micro --master-size t2.micro --master-volume-size 8 --node-volume-size 8 --dns private --dns-zone=Z009773516SA19I6MGD7H
kops update cluster  $NAME --yes --admin
kops export kubecfg $NAME
kubectl get nodes
</pre>
