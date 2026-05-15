# Commands Used in Project

## Connect Public EC2

```bash
ssh -i key.pem ec2-user@PUBLIC-IP
```

---

## Connect Private EC2

```bash
ssh -i key.pem ec2-user@PRIVATE-IP
```

---

## Check Current Directory

```bash
pwd
```

---

## List Files

```bash
ls -l
```

---

## Change Permission

```bash
chmod 400 key.pem
```

---

## Create Test File

```bash
echo "Hello S3" > test.txt
```

---

## Check S3 Buckets

```bash
aws s3 ls
```

---

## Upload File to S3

```bash
aws s3 cp test.txt s3://bucket-name/
```

---

## Copy File to Private EC2

```bash
scp test.txt ec2-user@PRIVATE-IP:/home/ec2-user/
```
