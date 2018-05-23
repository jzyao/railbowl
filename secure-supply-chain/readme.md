---
type: reference
version: 1.0
title: ��Docker �ο��ܹ������� Docker ��ȫ��Ӧ��"
redirectfrom:
  - "Docker_Reference_Architecture-_Building_a_Secure_Supply_Chain"
  - docker-reference-architecture-building-a-secure-supply-chain
  - secure_supply_chain
summary: �����ο��ܹ������˰�ȫ��Ӧ����������������ʹ�� Git��Jenkins �� Docker �̵���Ϊ��Ӧ���ṩ�زġ�����ʹ��ͬ�๤��������ο��ܹ����г��ĺ�����ʾ�������й��ߡ�"
dateCreated: ��Wed, 11 Oct 2017 04:08:00 GMT"
dateModified: ��Wed, 11 Oct 2017 04:08:00 GMT"
dateModified_unix: 1507694880
upvote: 0
author: "clemenko"
platform:
  - linux
  - windows server
testedon:
  - ee-17.06.1-ee
  - dtr-2.3.0
  - ucp-2.2.0
tags:
  - security
product:
  - EE
---

<a name="introduction"></a>
## ���
������ȫ�ľ���Ӧ��������Ҫ��ÿ����֯����ҪȨ�����п���ѡ��˽ⰲȫ���ա��ɹ�ѡ��ľ����������������ѡ�Ѷȡ������ף�ÿ����֯����Ҫ�˽����о������Դ����ʹ���������� [store.docker.com](http://store.docker.com) �еĿ����ϵ����ξ���ʱҲ����ˡ�������������ܹ��󣬺��б�Ҫ����©��ɨ�衣��������ɨ�蹦�ܵ� Docker Trusted Registry �ṩ�������˽�©�����������������������Ҫʵ���Զ������ṩ�򵥵����������

<a name="what-you-will-learn"></a>
## ѧϰ����
���ο��ܹ������˰�ȫ��Ӧ����������������ʹ�� Git��Jenkins �� [Docker �̵�](http://store.docker.com)��Ϊ��Ӧ���ṩ�زġ�����ʹ��ͬ�๤��������ο��ܹ����г��ĺ�����ʾ�������й��ߡ���ȫ��Ӧ���ɷ�Ϊ�����׶Ρ�

- �׶� 1 �Ǵ��뾵��ֿ⡣
- �׶� 2 �ǳ������ɡ�
- �׶� 3 �ǿ���ɨ�辵��ľ���⡣

���ܴ������������ߣ����ĵ���Ҫ��עһ�鹤����ϣ�

- GitLab���׶� 1��
- Jenkins���׶� 2��
- Docker Trusted Registry���׶� 3��

���ڴ˲ο��ܹ�����Ҫ�μ�һ�����ԡ��κ��˶����ý���������ֱ��Ͷ�������Ĵ��룡��

<a name="prerequisites"></a>
## �Ⱦ�����
�ڼ���֮ǰ,�����˽Ⲣ��Ϥ��

- [Docker �ĵ��е� Docker ����](https://docs.docker.com/engine/understanding-docker/)
- [���� Docker EE ��ȫ�Ͱ�ȫ���ʵ��](https://success.docker.com/Architecture/Docker_Reference_Architecture%3A_Securing_Docker_EE_and_Security_Best_Practices)

<a name="abbreviations"></a>
## ��д��
�ڱ��ĵ���ʹ����������д�ʣ�

* UCP = Universal Control Plane
* DTR = Docker Trusted Registry
* DCT = Docker Content Trust
* EE = Docker ��ҵ��
* RBAC = Role Based Access Controls�����ڽ�ɫ�ķ��ʿ��ƣ�
* CA = Certificate Authority����֤���ģ�
* CI = Continuous Integration���������ɣ�
* CD = Continuous Deployment����������
* HA = High Availability���߿����ԣ�
* BOM = Bill of Materials�������嵥��
* CLI = Command Line Interface�������нӿڣ�

<a name="why"></a>
## ԭ��
����Ҫ**��ȫ��Ӧ��**��ԭ���ж�����������Ͻ�������Ϊ������������**��ȫ��Ӧ��**�������������ܵ�Ҳ����ӵ���Զ���������������档�ᵽ��Ӧ�����͸�����һЩ��Ҫ�ĸ��ԣ�

* ���κ��˶����ý���������ֱ��Ͷ�������Ĵ��룡��
   * �����Է�ֹ�������͵͵����ͨ����˵Ĵ��롣��Ҳ������Ԥ���ڲ���в��
* ��һ�ж���Ҫ�����������
   * ����ܹ�֤�����ݡ�ʱ�䡢ԭ�򡢺ͷ�ʽ���Ϳ��Լ�ÿ���˵Ĺ�����
* ��ÿ����Ӧ������Ҫ��֪��ȫԴͷ����
   * �����������н�����

��������£���ϣ���������ٶȻ�ȡ������ϣ��ȷ������ĳɹ����Թᴩ������Ӧ����������Ҫ��ȡ�Ĳ���������Ǽ����ƶ���������뷽ʽ������������ֻ��Ҫ���������_Git (GitLab) �� Docker Trusted Registry (DTR)_��

�����ǵ���;���Ļ���ͼ��
![](./images/supply_chain_workflow.png)

<a name="begin"></a>
## ��֪�ɿ�Դͷ
���۹�Ӧ���ж��ɫ�����������ڴӡ���֪�ɿ�Դͷ�����֡��׶� 1 ���Է�Ϊ�������ܵ���㡣

- ���� [Docker �̵�](https://store.docker.com)���Զ�����Ԥ�ơ�����ã�����֤�ľ���
- ���� Dockerfile ������ YAML ��˽�а�ȫ Git ����ֿ�

���߶��г�ֵ����ɡ�Docker �̵�;����ζ�ż̳����ξ���ʱ����������գ�ȡ���ڹ�Ӧ�̹������ķ�ʽ��Git ;����ζ�Ź�������ʱ��Ҫð���ա�������ڵ㶼������ȱ�㡣������㶼�п���֤�����ݣ���ȷ�������ǡ���֪�ɿ�Դͷ����

�ں���Ĳ��ֽ�������Դͷ������ϸ���ܡ�

<a name="store"></a>
## Docker �̵�
Docker �̵� [store.docker.com](https://store.docker.com)Ӧ���ǲ��Ҿ������ѡλ�á���Ϊÿ����֯�ṩ�˾޴�����ơ��̵��а���������ʱ���õľ��񡣾��������߸�����¾���ȷ��������©����Docker �̵걣֤�����С���֤���͡��ٷ������񶼾���©��ɨ�衣���ڡ���֤������Docker �̵�͹�Ӧ�̻����ȡ����һ���Ĵ�ʩ����֤������Ҫ����[ȫ����������](https://success.docker.com/Store)���Ӹ�����˵����Ӧ�̺� Docker Ϊ��֤�����ṩ���������������ı�֤���̵��л��������ٷ������񡣡��ٷ��������� Docker ����������Ҳ���� Docker ���ڽ��и��¡�Docker �̵�� [Docker Hub](https://hub.docker.com) �л������������񡣷��򲻵�������ʹ����������

![](./images/store.docker.com.jpg)

<a name="upstream"></a>
### ��ѡ���ʵ����ξ���
��[�̵�](https://store.docker.com)����ѡ���ʵľ���������Ҫ������ʹ���ĸ�����ľ��߹��̷ǳ��򵥡������ȿ���[��֤����](https://blog.docker.com/2017/03/announcing-docker-certified/)��Ȼ���ٿ��ǹٷ���������������������ʹ�á��Զ�������������������������ȷ�����ǻᱻ��ʱ���¡���֤��������ʶ�Ҳ����Ҫ��

�������ݽ�ѡ���й�[��֤����](https://blog.docker.com/2017/03/announcing-docker-certified/)��һƪ���ģ�

*Docker ��֤�ƻ�ּ�ڰ�����������������ҵ�ͻ�ʶ��������������֧�ּ��Ϲ��Է�����ֳ�Ⱥ�������Ͳ����Docker ��֤�Կ��õ� Docker EE �����ܹ�Ϊ��׼������ Docker �ͷ����ߵ�֧�֣�Ϊ��ҵ�ṩ�����������и��༼���Ŀɿ�;���������Զ��׼��Ļ��£��ͻ����Կ���ʶ�������֤�������Ͳ�������ҿ���ȷ�����ǲ������ʵ�������������Կ��� Docker EE ��ƽ�����С�*

![](./images/certified_program.jpg)

�� [Docker �̵�](https://store.docker.com)����������ʱ�������ѡ��** Docker ��֤**��ѡ��

![](./images/store_certified.jpg)

 [Docker �̵�](https://store.docker.com)��һ����ɫ�����Ǿ�����Ҫ������ȫ©��ɨ�衣�˹���ʹ��������ȡ����ǰ�ȶԾ�����м�顣

![](./images/store_nginx.jpg)

����[�̵�](https://store.docker.com)�еġ���֤������֮�⣬��һ�ֺܰ�����Դ�� Hub ��[���ٷ�������](https://hub.docker.com/explore/)��������Щ���ٷ����������Զ����ģ�����ɨ���Ҹ���Ƶ�ʺܸߡ�

��ʹ�÷ǹٷ���δ����֤�����ξ���ʱ����Ҫȷ������Ϊ�Զ�������һ����Ҫ�Ĳ�������� Dockerfile����ȷ�������н�������ȷ����λ���򲻵���ʱ���ɿ��Ǵ��������ġ��Զ�������

��ע�⣬�κδ� Hub ���̵�����ȡ�ľ���Ҳ��Ӧͨ�� Docker Trusted Registry ������ͬ�������ϸ��顣

<a name="gitlab"></a>
## Git - (GitLab CE)
���ִ���ҵ�У��汾����ϵͳ�������д��붼����Ҫ��Git �Ȱ汾����ϵͳҲ�Ǹ������õĳ�ɫ��ʽ��Git ������Ϊ����ҵ�ġ�����Դ������ҹ�˾����� Git ��������[GitLab CE](https://store.docker.com/images/gitlab-community-edition) �ǳ�ɫ�Ŀ�Դ Git ���������������ʾ����ʹ�õ��� GitLab �����档

����� Git ����ֿ�ṹ���������Ͳ�������������ļ���������˵������ `Dockerfile`������� `stack.yml`�� `Dockerfile` ���������� Docker ����ġ�ʳ�ס��������ļ�Ӧ�ü��׶��� `stack.yml` ��������Ӧ��ջ�� `stack.yml` Ҳ����Ϊ compose YAML �ļ��������ʾ��ʹ�� Docker ������һ�� GitLab ʵ������ʵ��Ӧλ������ Docker ��ҵ�漯Ⱥ�ⲿ��

<a name="gitlab_setup"></a>
### ����
���[ʹ�� Docker ��������](https://docs.gitlab.com/omnibus/docker/)��GitLab �ṩ��һЩ�ǳ��õ�˵����Git Ĭ��ʹ�� SSH �˿� (22)������������������ Git �Ķ˿ڡ�����չʾ����ν� GitLab �Ķ˿��ƶ��� 2022����������������˵���ƶ������� SSH �˿ڿ��ܸ�Ϊ�������⣬������״̬��װ����Ҫʹ�����ô洢��ʹ��Ӧ��ջ�ļ����򻯰�װ���̣�

```
version: "3.3"
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    ports:
      - 80:80
      - 443:443
      - 2022:22
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /srv/gitlab/config:/etc/gitlab
      - /srv/gitlab/logs:/var/log/gitlab
      - /srv/gitlab/data:/var/opt/gitlab
    restart: always
    networks:
      gitlab:

  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /srv/gitlab-runner/config:/etc/gitlab-runner
      - /root/.docker:/root/.docker
      - /root/.notary:/root/.notary
    restart: always
    networks:
      gitlab:

networks:
  gitlab:
```

���������ݱ���Ϊ `gitlab.yml`��Ȼ��ִ���������

```
$ sudo docker swarm init
$ sudo docker stack deploy -c gitlab.yml gitlab
```

ע�⣬GitLab ��Ҫһ������������

��һ���ֽ����ܾ���ֿ����ݡ�

<a name="repo_contents"></a>
### ����ֿ�����
���á�����Դ����һ���÷����ǽ����ɾ��������������Ӧ��ջ�洢��һ�𡣸�ʾ��Ϊ����Ӧ�ã��ɡ�Web������middleware���͡�db����ɡ����������� Dockerfile �� `stack.yml` �洢����ͬ��λ�÷ǳ����������Ŷӽ��й����Ͳ��������ϣ�Ŀ¼�ṹ��Ӧ������

1. ����Ϊ `web.dockerfile`��`middleware.dockerfile` �� `db.dockerfile` �� Dockerfile��
2. ÿ���Դ��λ�͹�����λ����һ��Ŀ¼�У���
3.  `stack.yml`������ `docker stack deploy`����
4. GitLab CI ���� `.gitlab-ci.yml`�����潫���ܣ���

�������� Dockerfile ��ʹ�ö�׶ι�������׶ι��������ڴ����С���ɵľ���Ĵ�С�����ڣ��������Զ�ʵ����һ�С�

![](./images/gitlab_dir_contents.jpg)

<a name="gitlab_ci"></a>
## ���������Զ���
Ϊ�˼��С��κ��˶����ý��������͵��������������������Ҫ����һ���Զ������������°汾�� GitLab������ֱ������ CI/CD ���ܡ�������ʹ���������ߡ�����������ù��̺Ϳ�ά���ԡ�Ҫ���� CI/CD������������ע��һ�����г������г��������ǰһ���������е� `gitlab.yml` �С���һ�����������г���

<a name="gitlab_config"></a>
### �������г���
������ CI �������ƣ�GitLab ҲҪ��ʹ�����г�������ɹ�����Ҫ������ǰһ�������� `docker stack deploy` һ��װ�����г�����Ҫ��ȡ���г������ơ������**��������** --> **���г���**���ڴ˴������ҵ�ע�����г�����������ơ�

![](./images/gitlab_add_runner.jpg)

ֵ�����ҵ��ǣ�����ע�����г���Ľݾ�����ͨ�� ssh ���� GitLab �ڵ㲢�������� Docker ����� GitLab CE ҳ��֪ͨ���ƣ���

```
docker exec -it $(docker ps --format '{{.Names}}\t{{.ID}}'|grep runner|awk '{print $2}') gitlab-runner \
register -n \
--url http://gitlab.example.com \
--registration-token <^>$token<^^> \
--executor docker \
--description "local docker" \
--docker-image "docker:latest" \
--docker-volumes "/var/run/docker.sock:/var/run/docker.sock" \
--docker-volumes "/root/.docker:/root/.docker"
```

ע����ɺ������������г�����ʾ`�ѹ���`��ǩ��������Ҫȷ��ѡ����`�����ޱ�ǩ��ҵ`��δѡ��`��������ǰ��Ŀ`��

<a name="gitlab-yml"></a>
### ��������
CI ���ڴ˴��������á�GitLab ��������ֿ��Ŀ¼������Ϊ `.gitlab-ci.yml` ���ļ������ļ����� CI �����ļ���

> **ע��**�йش����⣬��鿴 [GitLab �ĵ�](https://docs.gitlab.com/ee/ci/yaml/README.html)��

��˼�����������������ڸ�������ֿ������ñ�����

�����Ǵ� git ���������������ʾ����

```
# Official docker image.
variables:
  DOCKER_DRIVER: overlay2

image: docker:latest

before_script:
  - docker login -u <^>$DTR_USERNAME<^^> -p <^>$DTR_PASSWORD<^^> <^>$DTR_SERVER<^^>

build:
  stage: build
  script:
    - docker build --pull -t dtr.example.com/admin/"<^>$CI_PROJECT_NAME<^^>"_build:<^>$CI_JOB_ID<^^> .
    - docker push dtr.example.com/admin/"<^>$CI_PROJECT_NAME<^^>"_build:<^>$CI_JOB_ID<^^>
    - docker rmi dtr.example.com/admin/"<^>$CI_PROJECT_NAME<^^>"_build:<^>$CI_JOB_ID<^^>
```

������������ȡ����ӱ�ǩ�Լ�������� store.docker.com ���͵� DTR �� `.gitlab-ci.yml` ������ʾ����

```
# Official docker image.
variables:
  DOCKER_DRIVER: overlay2

image: docker:latest

before_script:
  - docker login -u <^>$DTR_USERNAME<^^> -p <^>$DTR_PASSWORD<^^> <^>$DTR_SERVER<^^>

stages:
  - signer

signer:
  stage: signer
  script:
    - docker pull <^>$DTR_SERVER<^^>/admin/flask:latest
    - export DOCKER_CONTENT_TRUST=1
    - docker push <^>$DTR_SERVER<^^>/admin/flask:latest
    - docker rmi <^>$DTR_SERVER<^^>/admin/flask:latest
```

![](./images/gitlab_add_variable.jpg)

���ڣ���Ϊ GitLab ���úñ����͹�����������������Ϊ��Ŀ��Ӵ�������

<a name="gitlab-triggers"></a>
### �ܵ�������
GitLab ���ڰ�����ɫ�� CI �����Լ�Զ�̴����ܵ��ķ�����GitLab �������Щ��������������Щ����������ķ����������**��Ŀ** --> **����** --> **CI/CD** --> **�ܵ�������** --> **չ��**��

![](./images/gitlab_triggers.jpg)

�˴�Ϊ��������ʽ��ʾ����`http://gitlab.example.com/api/v4/projects/<^><PROJECT><^^>/trigger/pipeline?token=<^><TOKEN><^^>&ref=<^><REF><^^>`.�˴�����������ֶ�Ϊ `<^><PROJECT><^^>`��`<^><TOKEN><^^>` �� `<^><REF><^^>`��`<^><REF><^^>` Ӧ����Ϊ��֧���ơ�`<^><TOKEN><^^>` Ӧ����Ϊ���� GitLab ��ȡ�����ơ���ȡ `<^><PROJECT><^^>` ����ѷ����Ǹ���**�ܵ�������**ҳ���е� URL���������Ժ��õ��ܵ���������

����������� Docker Trusted Registry��

<a name="dtr"></a>
## Docker Trusted Registry
Docker Trusted Registry �������Ǽ򵥵ľ���⡣DTR ���ڰ���һЩ��ǿ����Ӧ���ĳ�ɫ���ܡ��¹��ܰ������������Ͳ��ɱ��Եȡ�

�ڽ������Ĳ����н����ܲ����¹��ܡ�

<a name="dtr-scan"></a>
### ����ɨ��
�� �汾 2.2.0 ��ʼ��DTR �������ؾ���ɨ�蹦�ܡ�DTR �еı���ɨ�����潫���� [CVE ���ݿ�](https://cve.mitre.org/)ɨ�辵�����ȣ�ɨ����򽫶Ծ����ÿһ��ִ�ж�����ɨ�裬ʶ��ÿһ���е�������������ÿ������� SHA �����������ö�����ɨ��������λ�ض����������������˴���©��������ܻᱻ���֣��������ǵ��ļ���Ϊ�Σ����������Ƿ�����ڷַ��嵥��������������У����������Ǿ�̬���ӵ�������Ƕ�̬���ӵ��������ʹ�������Ի������� OS �ַ�Ҳ�����⡣

Ȼ���ɨ��Ὣÿ������� SHA �� CVE ���ݿ⣨��֪��Ϣ��ȫ©���ġ��ֵ䡱�����бȽϡ��� CVE ���ݿ����ʱ��ɨ����������ѱ�������������Է������·��ֵ�©��ƥ�������������ɨ����ڼ�����֮����ɣ����ǽϴ�ľ���ֿ������Ҫ�ϳ���ʱ�����ɨ���꣬����ȡ����ϵͳ��Դ��ɨ�������ṩ��ɨ�����о�������ĵ㲢�ṩ�����嵥 (BOM)���ɽ����� [Notary](#dtr-notary) �����ȷ��Ϊ�����ṩ���䰲ȫ�Ĺ�Ӧ����

�� DTR 2.3.0 ��ʼ����ɨ������Ҳ����ɨ�� Windows �������ļ���

![](./images/secure_scan.png)

<a name="dtr-scan-setup"></a>
#### ���þ���ɨ��
��ʼ֮ǰ����ȷ������������֯�Ѿ������˰��� Docker ��ȫɨ�蹦�ܵ� DTR ���֤���������� Docker ID ������ Docker �̵��з��ʺ����ش����֤��

Ĭ������£������ð�ȫɨ��ʱ��ÿ��ִ�� `docker push`���¾���ֿⶼ���Զ�ִ��ɨ�裬���ǣ�Ĭ������£��κ�������ɨ��ǰ���Ѵ��ڵľ���ֿⶼ�ᱻ��Ϊ���ֶ�ɨ�衱ģʽ�������Щ����ֿ�����ʹ�ã��������ÿ������ֿ��**����**ҳ���и��Ĵ����á�

Ҫ���þ���ɨ�裬ת��**���� --> ��ȫ**��ѡ��**����ɨ��**��Ȼ��ѡ��ʹ�� Doker �ṩ�� CVE ���ݿ⣨**����** �� Ĭ��ѡ�����ʹ�ñ����ϴ��ļ���**�ѻ�** �� ����δ�����Ļ������޷����ӵ� Doker ���г������µĻ����Ƽ���ѡ���

������ģʽ���ú�DTR ���� Docker ���� CVE ���ݿ⣬�״�ͬ�����ܻỨ�ѽϳ�ʱ�䡣������İ�װ�޷����� `https://dss-cve-updates.docker.com/`���������ֶ��ϴ�������ȫ���ݿ�� `.tar` �ļ���

* �����ʹ�õ���**����**ģʽ��DTR ʵ���ͻ����� Docker ���������������µ�©�����ݿ⣬Ȼ����а�װ����װ��ɺ��������ʼɨ�衣
* �����ʹ�õ���**�ѻ�**ģʽ������**����ɨ�����ݿ� - �ѻ�ģʽ**�е�˵���ϴ���ʼ��ȫ���ݿ⡣
![](./images/dtr-scan-setting.jpg)

<a name="dtr-scan-offline"></a>
#### CVE �ѻ����ݿ�
��� DTR ʵ���޷����Ӹ��·��������������ز���װ�������ݿ���µ� `.tar` �ļ����ɴ� [Store.docker.com](https://store.docker.com) ��**�ҵ�����** --> ���֤**����**���ҵ���Щ�ѻ� CVE ���ݿ��ļ���

![](./images/store-license.jpg)

<a name="dtr-scan-results"></a>
#### ɨ����
Ҫ�鿴ɨ��Ľ�������������ֿⱾ��Ȼ�󵥻�**����**��δ����©���ľ���ɨ�����ʾ��ɫ�Ĺ��Ŷ���ͼ�꣺

![](./images/dtr-scan-clean.jpg)

����©���ľ���ɨ�����ʾ��ɫ�ľ�����ƣ�

![](./images/dtr-scan-vuln.jpg)

����ɨ����������������ͼ��**��**��**���**��**��**��ͼ��ʾ������ĸ����Ͼ��д���©���Ķ������ļ���Ҫ���©��λ�� Dockerfile �δ�ʱ������ͼ�������á�

![](./images/dtr-scan-binary.jpg)

�����㱾��ʱ������ʾ����©���Ķ������ļ����Լ��ò��ϵ������������ݡ��ڱ�ʾ���У����ڼ������ܴ���©���Ķ������ļ���

![](./images/dtr-scan-layer.jpg)

��������©���ľ������鿴 **���** ��ͼ����**���**��ͼ�У��ɲ鿴 CVE ��š�ָ�� CVE ���ݿ�����ӡ��ļ�·������Ӱ��Ĳ㡢�����Ժ�������˵����

![](./images/dtr-scan-component.jpg)

���������ԶԴ���©���Ķ������ļ�/��/�����ȡ������

��������˴���©��������������Ƿ���ڰ�ȫ©���Ѿ��޸��ĸ��º�汾�����б�Ҫ������ϵ�����ά����Ա��ȷ����δ���İ汾�򲹶��������޸���©����

�����©��λ��`������`���������ϵͳ���У��������޷����������е����⡣����������£���������Ҫ�л�����һ��������汾�����߿���Ѱ��һ�������൱����̫�����ܵ������Ļ����㡣��Ҳ����ȷ���Ƿ���Խ��ܸ�©����¶��

ͨ��ʹ�ô���©����������Ѹ��º��Ѹ����汾����ʹ�ù�����ͬ�Ĳ�ͬ��������������ֿ��е�©�����⡣�����Ѹ���Դ����ʱ��������һ�������Դ����¾��񣬱�Ǿ��񲢽����º�ľ������͵����� DTR ʵ����Ȼ������������ɨ��þ�����ȷ�������޸���Щ©����

������©������ʱӦ����ô�죿ʵ�����������׶Ρ���һ�׶���Ϊ����Ķ������ļ��Ͳ����ɹ�ϣֵָ�ơ��ڶ��׶��ǽ���Щ��ϣֵ�� CVE ���ݿ���бȽϡ�����ָ�ƽ׶κ�ʱ��á��ȽϹ�ϣֵ�ܿ�Ϳ�����ɡ������µ� CVE ���ݿ�ʱ��DTR ֻ�轫���й�ϣֵ���µ����ݿ���бȽϼ��ɡ��˹��̺�ʱҲ�϶̡�ɨ������һֱ���¡�

�����Ѿ�����ɨ��������������������������ˡ�

<a name="dtr-promotion"></a>
### ������������
Docker Trusted Registry 2.3.0 ���а���������µ���������ķ����������Ի���©������ֵ����ǩƥ�䡢����������������֤�����������ԡ������Զ����������з��Ӿ޴����á���Ҳ����ȷ������Բ�ƥ��ľ����޷����������������������£�

* ��ǩ����
* ���������
* ����©��
* �ؼ�©��
* �ش�©��
* ΢С©��
* �������֤

![](./images/dtr-promotion.jpg)

���ɴ������Բ���Դ��Ŀ����в鿴��������**����©��**��ʾ�������������������©��ʱ�� `admin/flask_build` ����ֿ⡰�������� `admin/flask` ���������ԡ�

���ȣ������Դ����ֿ⣬Ȼ��ת��**����**ѡ����Ӹ�ѡ��У�ѡ��**����������**������࣬ѡ��**����©��**��Ȼ�󵥻�**С�ڻ����**�����ı��������� `0`���㣩��������**���**�����ڣ�Ϊ����������ѡ��Ŀ�ꡣ���Ҳ࣬ѡ��Ҫ��ΪĿ��������ռ�;��񡣵���**���沢Ӧ��**��Ӧ��Ҫ��Դ����ֿ�ִ�еĲ��ԡ�**����**�Ὣ����Ӧ�õ�δ�������͡�

������**Ŀ���еı�ǩ����**����ѡ���ṩ���ݲ��ֱ������ı�ǩ����������������ʼʱ��Ҫ���ı�ǩ���ơ��йظ�����Ϣ����鿴[�������������ĵ�](https://docs.docker.com/datacenter/dtr/2.3/guides/user/create-promotion-policies/)��

![](./images/dtr_promo_policy.jpg)

������**������**���¡���Ҫע���һ���� Notary ǩ�����澵��һ������������ζ�� CI ϵͳ������������ľ������ǩ�������ͨ��ʹ�� webhook ��������������ɡ�

![](./images/dtr_promoted.jpg)

���ǻ�������� Jenkins ���͵� DTR ʱ�� DTR ���á�������������©�� �� ���õ�**��ȫ��Ӧ��**��һ���֣���ô��ɨ�貢�������ǡ����Ҫ˵�����񲻿ɱ����ˡ�

<a name="dtr-immutability"></a>
### ���񲻿ɱ���
DTR 2.3.0 �͸��߰汾���н�����ֿ�����Ϊ**���ɱ��**��ѡ�������ֿ�����Ϊ**���ɱ��**��ζ�ű�ǩ���޷������ǡ�����һ���ܺõĹ��ܣ��ɱ�֤�������񲻻�����ʱ������Ƹ��ġ���ʾ��Ϊ Alpine ����������������£�CI �����»������񲢽������͵� DTR�������ض���ǩ��������**���ɱ���**�ɱ�֤���ۺ�ʱ�鿴�ض���ǩ������������ġ���ʹ�þ��������������Դ˹��ܽ�����չ��

![](./images/dtr-immutable.jpg)

<a name="dtr-immutability_policy"></a>
### ���񲻿ɱ��� + ��������
�Բ��ɱ���ı�ǩʹ���������Ե�һ���ܺõ�ʾ����ֱ�Ӵ� Git �������񡣱�ʾ��ʹ���˼򵥵� flask Ӧ�á�`.gitlab-ci.yml` ���������������裺���������͡�ɾ�����������˲��ɱ��Ե� DTR ����ֿ� `dtr.example.com/admin/flask_build`���͵�˽�о���ֿ⡣

![](./images/dtr_pub_repos.jpg)

GitLab ����ʱʹ�ù�������Ϊ��ǩ����ʽ�����ڣ�`dtr.example.com/admin/flask_build:66`.���������˲��ɱ��ԣ��޷����Ǳ�ǩ `66`���⽫�춨�ι̵Ļ�������������������ͬ����ֵ��������������ԡ���һ�����Խ��������������о���ֿ� `dtr.example.com/admin/flask`����ǩ���䡣�ڶ�������ʹ��`���µ�`��ǩ����������

![](./images/dtr_flask_policies.jpg)

����ǽ����������������Ρ�һ��ʹ����ͬ�ı�ǩ����һ��ʹ��`���µ�`��ǩ��

![](./images/dtr_promoted.jpg)

��Ӧ���е���һ�������� webhook��

<a name="dtr-webhook"></a>
### Webhook
�� DTR 2.3.0 ��ʼ����ͨ�� GUI ���� webhook��DTR �������������±�ǩ��ɾ������ȳ����¼��� webhook����ʹ�����Դ��Լ��� DTR ��Ⱥ�������ӵ� CI �� CD �ܵ���
���ɶ��ĵ� webhook �¼����£��ض��ھ���ֿ⣩��

* ��ǩ����
* ��ǩɾ��
* �嵥����
* �嵥ɾ��
* ��ȫɨ�������
* ��ȫɨ��ʧ��
* �Ӿ���ֿ������ľ���

Webhook �ǰ�����ֿⴴ���ġ����� [Docker �ĵ�](https://docs.docker.com/datacenter/dtr/2.3/guides/user/create-and-manage-webhooks/)���ҵ��й� webhook �ĸ�����Ϣ��DTR Ҳ���Գ��� API����������������Ϸ���¼����Ĳ˵���Ȼ��ѡ�� **API �ĵ�**��

��ʾ����ʹ�� `dtr.example.com/admin/flask_build` ����ֿ��ǰһ��ʾ���ĺ������֡����ڣ���� webhook��Ҫʹ�á��Ӿ���ֿ������ľ����¼���ӣ����뽫 webhook ����Ϊ��֪ͨ GitLab ʹ�� Notary �Լ��Ծ������ǩ����

![](./images/dtr-webhook.jpg)

Ϊ���ṩ�ο�������ʹ�õ� `WEBHOOK URL` ��`http://gitlab.example.com/api/v4/projects/<^><PROJECT><^^>/trigger/pipeline?token=<^><TOKEN><^^>&ref=<^><REF><^^>`���˴�����������ֶ�Ϊ <^><PROJECT><^^>��<^><TOKEN><^^> �� <^><REF><^^>��<^><REF><^^> Ӧ����Ϊ��֧���ơ�<^><TOKEN><^^> Ӧ����Ϊ���� GitLab ��ȡ�����ơ�<^><PROJECT><^^> �ɴӴ���������ҳ���ȡ������[������]��#gitlab-�������������ҵ��йش������ĸ�����ϸ��Ϣ��webhook �ʹ����������������ǿ�����������ҵ���뾵��ǩ�����ơ�

<a name="dtr-notary"></a>
### Content Trust/����ǩ�� �� Notary

*Notary �ǿ����������͹�����ŵ����ݼ��ϵĹ��ߡ������߿ɶԼ��Ͻ�������ǩ����ʹ���߿�����֤���ݵ������Ժ���Դ���˹��ܻ��ڼ򵥵���Կ�����ǩ�����湹�����ɴ�������ǩ���ļ��ϼ����������εķ����ߡ�*

Docker Content Trust/Notary Ϊÿ�������ṩ�˼���ǩ����ǩ���ṩ�˰�ȫ�ԣ��������ȡ�������������ľ���������Ա��� Notary ��ȫ�ķ�ʽ����Ȥ������� [Notary �ļܹ�](https://docs.docker.com/notary/service_architecture/)������ Docker EE �ڡ�Ĭ��������ǰ�ȫ�ġ���Docker Trusted Registry �渽���伴�õ� Notary ��������

�ɹ�ǩ���ľ����� DTR GUI �о�����ɫ�Ĺ��ű�־��

![](./images/dtr-signed.jpg)

<a name="gitlab_sign"></a>
##### ʹ�� GitLab ǩ��
�����ŶӲ���׳�󣬹������п�������ԿҲ���Խ��Խ���ѡ���������������ɵķ���֮һ�ǽ�ֹ�����߶Ծ������ǩ����ʹ�� GitLab ���Լ��������������������о������ǩ����ʡȥ�󲿷���Կ��������������Ҫ�� GitLab �������ϵ���Կ���б����ͱ��ݡ�

��һ����Ϊ���� CI ϵͳ�����û��ʻ������磬������ʹ�õ� CI ϵͳ�� GitLab������� DTR �� Web ���档�Թ���Ա�û���������**��֯**��Ȼ��ѡ��**����֯**��������֯����Ϊ `ci`�����ڣ���� Jenkins �û������������������֯����ѡ��**����û�**��������Ϊ `gitlab` ���û�������ǿ���롣�˲����������û���������ӵ� `ci` ��֯������������ GitLab �û�����`��֯����Ա`��ݣ���ʹ�û����Թ��� `ci` ��֯�µľ���ֿ⡣�ٽ������������ UCP ��**�û�����**���� `ci` ��֯�´�����Ϊ `gitlab` ���Ŷӡ�

![](./images/dtr_add_user.jpg)

�����ŶӾ����ú��ˣ���Ҫ���ò���ʵʩ�������**����Ա����**��Ȼ��ѡ�� **Docker Content Trust** ���֡�ѡ��**��������ǩ��ľ���**��ѡ�������� Docker Content Trust���ڳ��ֵ�ѡ����У�ѡ��ղŴ����� `ci` �Ŷӡ��������á�

�˲���Ҫ�� `docker pull`��`docker run` �� `docker service create` �����õ�ÿ������������ `gitlab` �Ŷӵĳ�Ա��Ӧ����Կǩ�����ڴ�ʾ���У�Ψһ�ĳ�Ա���û� `gitlab`��

![](./images/ucp-signing-policy.jpg)

ǩ������ʵʩʹ���û��ͻ���֤����а䷢��֤�齫ǩ�����û��������ʹ���޺���������ڣ�����������ʽ����¼����ǰ�������û��ʻ� `gitlab`��Ϊ���û�����һ���ͻ���֤��������⣬�������������� UCP �д洢�Ĺ�Կ�������˵�����Ա��������ܹ�ʶ������ǩ�������ĸ���Կ��

��ע�⣬ÿ���û������µĿͻ���֤���ʱ����������һ���µ���Կ�ԡ���ˣ��б�Ҫ���û�ѡ��ָ��Ϊ��ǩ��֤������ض�֤������и��١�

��ѹ���ͻ���֤�����ֻ��Ҫ�������ļ�����ǩ����`cert.pem` �� `key.pem`�����Ƿֱ�������û���ǩ����ݵĹ��ú�˽�в��֡��� `key.pem` �ļ����ص� Jenkins ����������ʹ�� `cert.pem` �� Trusted Collection ��Ϊ�û� `gitlab` ����ί�ɡ�

�� GitLab �������ϣ���䱾�� `/root/.docker/` Ŀ¼��Ȼ���ʼ���� notary ����ֿ⡣�� `gitlab` �������ϣ�ִ�����²�����

```
#become root
sudo -i

#change to the /root directory
cd /root

#make directory for CA
mkdir -p /root/.docker/tls/dtr.example.com/

#copy CA to the notary/docker location
curl -sk https://dtr.example.com/ca -o /root/.docker/tls/dtr.example.com/ca.crt

#get the notary client
wget https://github.com/docker/notary/releases/download/v0.4.3/notary-Linux-amd64

#chmod it
chmod 755 notary

#setup alias
alias notary="notary -s https://dtr.example.com --tlscacert /root/.docker/tls/dtr.example.com/ca.crt -d ~/.docker/trust"

#check notary
./notary key list

#init the repository.Make all the passwords the same to simplify things.
notary init -p dtr.example.com/admin/flask

# rotate snapshot key
notary key rotate dtr.example.com/admin/flask -r snapshot

# setup releases role
notary delegation add dtr.example.com/admin/flask targets/releases cert.pem --all-paths

# setup user role
notary delegation add dtr.example.com/admin/flask targets/gitlab cert.pem --all-paths

# publish changes
notary publish dtr.example.com/admin/flask
```

Ҫ�����Զ�ǩ������Ҫ�� GitLab ��Ŀ�������롰�����������������Ƶ� `DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE` ����������������������ǩ���� GitLab ��Ŀ��ʹ������� `.gitlab-ci.yml` ��������Ŀ������Ŀʹ�ñ��� `/root/.docker/trust` Ŀ¼��

```
# Official docker image.
variables:
  DOCKER_DRIVER: overlay2

image: docker:latest

before_script:
  - docker login -u <^>$DTR_USERNAME<^^> -p <^>$DTR_PASSWORD<^^> <^>$DTR_SERVER<^^>

stages:
  - signer

signer:
  stage: signer
  script:
    - docker pull <^>$DTR_SERVER<^^>/admin/flask:latest
    - export DOCKER_CONTENT_TRUST=1
    - docker push <^>$DTR_SERVER<^^>/admin/flask:latest
    - docker rmi <^>$DTR_SERVER<^^>/admin/flask:latest
```

ǩ����Ŀ�ĳɹ����Ӧ����������Ľ�ͼ��

![](./images/gitlab_completed_signing.jpg)

���ڣ����ļ���������Ϊ����ǩ����Ŀ�����ܵ���������ʹ�ô��С��Ӿ���ֿ������ľ����¼��� webhook��


<a name="conclusion"></a>
## �ܽ�
ʵ���Զ����� **��ȫ��Ӧ��** ��û����ô���ѡ���ѭ���ο��ܹ��Ϳ����ô�������������Ŀ�� GitLab��һ����Ŀ��Դ��롢Dockerfile ��Ӧ��ջ yaml����һ����Ŀ��Ծ���ǩ�������DTR Ҳ������������ֿ⡣һ�����˽�й�������һ����Ծ���ǩ�����������ľ���

��ҪĿ���ǹ���һ��**��������**������ȫ��ɨ�裩��**����ǩ����**���񣨴��� Notary����ͨ��**�Զ�**��ʽ����

![](./images/dtr_signed_promoted.jpg)

��Ȼ�������ض��Ĺ��ߣ����Ǳ��ο��ܹ��м����ؼ��㣺

* �Զ���һ��
* ��ѡ��֪�ɿ�Դͷ
* ���þ���ɨ�������
* �Ծ������ǩ��
* �κ��˶����ý���������ֱ��Ͷ�������Ĵ��룡

�����ǹ������عˡ�

![](./images/supply_chain_workflow.png)

��ϸ����һ����Щ�������ʹ����֯ӵ�еĹ������滻���𹤾ߡ�
