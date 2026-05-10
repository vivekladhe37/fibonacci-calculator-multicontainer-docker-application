# AWS Configuration Cheat Sheet

This cheat sheet provides a quick reference for configuring AWS services (Elastic Beanstalk, RDS, ElastiCache) and Travis CI for a multi-container Docker application.

## 1. Elastic Beanstalk Application Creation
1. Go to **Elastic Beanstalk** in the AWS Console.
2. Click **Create Application**.
3. **Application Name**: `multi-docker`.
4. **Platform**: Select **Docker**.
5. **Presets**: Verify **Single Instance (free tier eligible)** is selected.
6. Click **Next**.
7. **Service Role**: Verify **Use an Existing service role** is selected.
8. **Service Role Name**: `aws-elasticbeanstalk-service-role`.
9. **EC2 Instance Profile**: `aws-elasticbeanstalk-ec2-role`.
10. Click **Skip to review**, then **Submit**.

## 2. RDS Database Creation (PostgreSQL)
1. Go to **RDS** -> **Create database**.
2. **Engine**: PostgreSQL.
3. **Templates**: **Free tier**.
4. **Settings**:
   - **DB Instance identifier**: `multi-docker-postgres`
   - **Master Username**: `postgres`
   - **Master Password**: `postgrespassword`
5. **Connectivity**: VPC set to **Default VPC**.
6. **Additional Configuration**:
   - **Initial database name**: `fibvalues`
7. Click **Create Database**.

## 3. ElastiCache Redis Creation
1. Go to **ElastiCache** -> **Redis OSS caches**.
2. Click **Create Redis OSS caches**.
3. Select **Design your own cache** and **Cluster cache**.
4. **Cluster Mode**: DISABLED.
5. **Cluster Info**: Name `multi-docker-redis`.
6. **Cluster Settings**:
   - **Node type**: `cache.t3.micro`
   - **Number of Replicas**: 0
7. **Subnet group**: Create a new group named `redis`.
8. Click **Next** until created.
9. **Crucial Step (Encryption)**:
   - Once "Available", click the cache name -> **Modify**.
   - Change **Transit encryption mode** from *Required* to **Preferred**.
   - **Preview changes** -> **Modify**.

## 4. Custom Security Group
1. Go to **VPC** -> **Security Groups** -> **Create Security Group**.
2. **Name/Description**: `multi-docker`.
3. **VPC**: Default VPC.
4. **Inbound Rules**:
   - Click **Add Rule**.
   - **Port Range**: `5432-6379`.
   - **Source**: Select the `multi-docker` security group itself (search for `sg-xxx`).
5. Click **Save rules**.

## 5. Applying Security Groups
### To ElastiCache:
- ElastiCache -> Redis clusters -> Select cluster -> **Actions** -> **Modify**.
- **Selected security groups** -> **Manage** -> Select `multi-docker`.
- **Preview** -> **Modify**.

### To RDS:
- RDS -> Databases -> Select instance -> **Modify**.
- **Connectivity** -> Select `multi-docker` security group.
- **Continue** -> **Modify DB instance**.

### To Elastic Beanstalk:
- Elastic Beanstalk -> Environments -> `MultiDocker-env` -> **Configuration**.
- **Instances** row -> **Edit**.
- **EC2 Security Groups** -> Check `multi-docker`.
- **Apply** -> **Confirm**.

## 6. Travis CI Configuration (`.travis.yml`)
Update your `.travis.yml` with the following deployment details:

```yaml
deploy:
  provider: elasticbeanstalk
  region: 'us-east-1' # Your specific region
  app: 'multi-docker'
  env: 'MultiDocker-env'
  bucket_name: 'elasticbeanstalk-us-east-1-923445599289' # Your S3 bucket
  bucket_path: 'docker-multi'
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key: $AWS_SECRET_KEY
```

## 7. Setting Environment Variables in Elastic Beanstalk
Navigate to **MultiDocker-env** -> **Configuration** -> **Updates, monitoring, and logging** -> **Edit**.

| Key | Value |
| :--- | :--- |
| **REDIS_HOST** | *Primary Endpoint from ElastiCache (omit :6379)* |
| **REDIS_PORT** | `6379` |
| **PGUSER** | `postgres` |
| **PGPASSWORD** | `postgrespassword` |
| **PGHOST** | *Endpoint from RDS Connectivity tab* |
| **PGDATABASE** | `fibvalues` |
| **PGPORT** | `5432` |

Click **Apply**.

## 8. IAM Keys for Deployment
1. Go to **IAM** -> **Users** -> **Create User**.
2. **User Name**: `docker-multi-travis-ci`.
3. **Permissions**: **Attach policies directly** -> `AdministratorAccess-AWSElasticBeanstalk`.
4. **Security Credentials** -> **Create access key** -> **CLI**.
5. Save the **Access Key ID** and **Secret Access Key**.

## 9. Travis CI Variable Setup
1. Go to Travis Dashboard -> Project Settings.
2. Add **Environment Variables**:
   - `AWS_ACCESS_KEY`
   - `AWS_SECRET_KEY`
   - `DOCKER_USERNAME`
   - `DOCKER_PASSWORD`

## 10. Deployment
1. Make a change in `src/App.js`.
2. Commit and push:
   ```bash
   git add .
   git commit -m "testing deployment"
   git push origin main
   ```
3. Monitor Travis CI and then Elastic Beanstalk for the green health check.

---
*Note: Always ensure the AWS region in `.travis.yml` matches your S3 bucket and environment region.*