## Terraform


provider "aws" {    
    region = "eu-west-1"

}
resource "aws_vpc" "my_vpc"{
        cidr_block = "10.0.0.0/16"

tags = {
        Name = "tech-230-jamie-vpc-terraform"}
}

resource "aws_subnet" "my_subnet" {
        vpc_id = aws_vpc.my_vpc.id
        cidr_block = "10.0.2.0/24"
        availability_zone = "eu-west-1a"
        tags = {
                Name = "tech230-jamie-subnet-terraform"
}
}
resource "aws_subnet" "private_subnet" {
    vpc_id = aws_vpc.my_vpc.id
    cidr_block = "10.0.3.0/24"
    availability_zone = "eu-west-1b"tags = {
        Name = "tech230-jamie-private-subnet-terraform"
    }
}
resource "aws_vpc_attachment" "my_attachment" {
    vpc_id = aws_vpc.my_vpc.id
    internet_gateway_id = aws_internet_gateway.my_igw.id
}

resource "aws_route_table" "public_route_table" {
    vpc_id = aws_vpc.my_vpc.id
    tags = {
        Name = "tech230-jamie-route-table-terraform"
    }
}

resource "aws_route" "public_route" {
    route_table_id = aws_route_table.public_route_table.id 
    destination_cidr_block = "0.0.0.0/0"
    gateway_id= aws_internet_gateway.my_igw.id
}

resource "aws_route_table_association" "public_association" {
    subnet_id= aws_subnet.public_subnet.id
    route_table_id = aws_route_table.public_route_table.id
}