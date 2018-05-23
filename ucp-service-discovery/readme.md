---
type: reference
version: 1.1.0
title: ��Docker �ο��ܹ���Universal Control Plane 2.0 �����ֺ͸��ؾ���"
redirectfrom: 
  - "Docker_Reference_Architecture-_Universal_Control_Plane_2.0_Service_Discovery_and_Load_Balancing"
  - docker-reference-architecture-universal-control-plane-20-service-discovery-and-load-balancing
  - ucp_service_discovery
summary: ���˽���ν�Ӧ�ò���Ϊ���񡢷����ֺ͸��ؾ�����μ򻯿�����Ա�����ɶ�̬���෢�ֵ�Ӧ�õĹ��̣��Լ� HRM ��λ��� DNS ������·�� HTTP ������"
dateCreated: ��Thu, 06 Apr 2017 03:58:13 GMT"
dateModified: ��Fri, 15 Sep 2017 16:51:35 GMT"
dateModified_unix: 1505494295
author: ��ahromis"
platform:
testedon:
  - ucp-2.1.1
  - ee-17.03.0-ee-1
tags:
  - appdev
product:
  - EE
---

## ���

�����ֶԷ������ע�Ტ������������Ϣ����ʹ���������˽�������ӵ���������Ӧ����΢������������ļܹ�ת�䣬�������Ѿ���Ϊ���зֲ�ʽϵͳ�ı�Ҫ��ɲ��֣���������Щ��������ά�����ԡ�

Docker ��ҵ�� (Docker EE) ���������ֺ͸��ؾ��⹦�ܣ���Ϊ������֯�Ŀ�����ά�ƻ��ṩ�����������ֺ͸��ؾ�����Է��㿪����Ա�����ɶ�̬���෢�ֵ�Ӧ�á����⣬��Щ���ܻ�������ά����ʦ��չӦ�õĹ��̡�

Docker ʹ�ó���**����**�ĸ���������Ӧ�á������ɻ�����ͬ�ľ��񴴽���������ɡ�ÿ���������ڹ����ڵ���ִ�в��һᶨ��Ӧ�õ�״̬��������ɡ��������ʱ���������ڴ�������ʱ��ɡ�����������ܶ���Ϣ�����а�������������������������Ķ˿ڡ����ӵ�����ʹӽڵ��������������Щ����ͬ��ɷ��������״̬�����ĳ���ڵ�δͨ������״�������������ĳ���ض��ķ�������δͨ������״����飬��ô��Ⱥ����������״̬��ʹ������һ������״�����õĽڵ�ķ���״̬һ�¡�Docker EE ���������֡����ؾ��⡢��չ�͵����¼�����ʹ�ñ��ſ����޷칤����

## ѧϰ����

�˲ο��ܹ����� Docker EE �ṩ�ķ����ֺ͸��ؾ�������Ľ����������������ʱ��Docker ʹ�� DNS ʵ�ַ����֣����� Docker �����˲�ͬ������·�ɣ���ȷ��Ӧ�ø߶ȿ��á�UCP 2.0 ���а��������µ�Ӧ�ò�����·�ɣ���Ϊ **HTTP ����·�� (HRM)**�������� DNS ������·�� HTTP �������Ķ��걾�ĵ�֮����������˽� HRM �Ĺ���ԭ���Լ������������ Docker �����ֺ͸��ؾ��⹦�ܼ��ɡ�

## ʹ�� DNS �ķ�����

Docker ʹ��Ƕ��� DNS ��Ϊ�ڵ��� Docker ���������е��������� Docker Swarm �����е�`����`�ṩ�����ֹ��ܡ�Docker ����߱��ڲ� DNS ����������Ϊ�û������ bridge��overlay �� MACVLAN �����е������ϵ����������ṩ���ƽ�����ÿ�� Docker ��������`����`��swarm mode �У������߱�һ���ɽ� DNS ��ѯת���� Docker ����� DNS �����������䵱 DNS ��������Ȼ��Docker ���潫�ڷ������������������ÿ�������ϼ�� DNS ��ѯ�Ƿ�����������`����`��������ڣ�Docker ���潫�����ֵ�洢�в�����������`����`����`����`��**����**ƥ��� IP ��ַ������ IP ��`����`���� IP (VIP) ���ظ����󷽡�

������*��������Ϊ��Χ*������ζ��ֻ������ͬ�����ϵ�����������ſ�ʹ��Ƕ��� DNS ���ܡ�������ͬ�����ϵ������޷������˴˵ĵ�ַ�����ң�ֻ�����ض������Ͼ߱�����������Ľڵ�Ż�洢������� DNS ��Ŀ������������߰�ȫ�Ժ����ܡ�

���Ŀ��������`����`��Դ����λ�ڲ�ͬ�������ϣ�Docker ����Ὣ DNS ��ѯת����Ĭ�� DNS ��������

![������](./images/DNS.png)

��ʾ���У���һ���������������ķ��񣬳�Ϊ `myservice`������ͬ�������ϻ����ڵڶ������� (`client`)��`client` �������� `curl` ������Ϊ `docker.com` �� `myservice`��������Ϊ��˷����Ĳ�����

* `client` Ϊ `docker.com` �� `myservice`���� DNS ��ѯ��
* ���������ý������� `127.0.0.11:53` ������ DNS ��ѯ�������䷢�͵� Docker ����� DNS ��������
* `myservice` ��������������� IP (VIP)�������ڲ�������䵽�������� IP ��ַ��������Ҳ����������ֻ������ֱ�ӽ������� IP ��ַ��
* `docker.com` û����Ϊ�������ƴ����� `mynet` �����У���������ת���������õ�Ĭ�� DNS ��������

## �ڲ����ؾ���

���� Docker Swarm ��Ⱥ�д�������ʱ�����Զ������������ IP (VIP)�����Ƿ���������һ���֡��������������ʱ������ VIP������ VIP �����������Զ����͵����� overlay �����ϸ÷������������״�����õ����񡣴˷����ɱ��������ڿͻ��˽����κθ��ؾ��⣬��Ϊ����һ�� IP ���ص��ͻ��ˡ�Docker �ᴦ������·�ɲ�������ƽ���ַ�������״�����õķ�������

![�ڲ����ؾ���](./images/ipvs.png)

Ҫ��ȡ����� VIP���밴���·�ʽ���� `docker service inspect myservice` ���

    # Create an overlay network called mynet
    $ docker network create -d overlay mynet
    a59umzkdj2r0ua7x8jxd84dhr

    # Create myservice with 2 replicas as part of that network
    $ docker service create --network mynet --name myservice --replicas 2 busybox ping localhost
    8t5r8cr0f0h6k2c3k7ih4l6f5

    # Get the VIP that was created for that service
    $ docker service inspect myservice
    ...

    ��VirtualIPs":[
                    {
                        "NetworkID":"a59umzkdj2r0ua7x8jxd84dhr",
                        "Addr":��10.0.0.3/24"
                    },
    ]

> DNS ��ѯ (DNS RR) ���ؾ����������ڷ���ʹ�� `--endpoint-mode`���ã�������һ�����ؾ���ѡ��� DNS RR ģʽ�£���Ϊÿ�����񵥶����� VIP��Docker DNS ������ͨ����ѯ��ʽ���������ƽ������������� IP��

## �ⲿ���ؾ��⣨Swarm Mode ����·�ɣ�

��������·���ʱ��������ʹ�� `--publish` ��־���ⲿ���������� Docker swarm mode �¹����˿���ζ�ż�Ⱥ�е�ÿ���ڵ㶼�������˿ڡ����ǣ��������������������ö˿ڵĽڵ���Ӧ����ô���أ�

�������������Ҫʹ������·�ɡ�����·���� Docker ���� 1.12 �е�һ���¹��ܣ����� `ipvs` �� `iptables` �����Դ�������ǿ���ȫ��Ⱥ����� (L4) ���ؾ����������������� swarm �ڵ���ܷ��񷢲��Ķ˿��ϵ����ӡ��κ� swarm �ڵ��յ�����������`����`���ѷ��� TCP/UDP �˿ڵ�����ʱ������ʹ�ó�Ϊ`���`��Ԥ���� overlay ���罫����ת��������� VIP��`���`�������Ϊ��ʽ������ overlay �������ƣ�������Ψһ��Ŀ���ǽ�����·���������ⲿ�ͻ��˴������Ⱥ������ʹ����ǰһ������������ͬ�Ļ��� VIP ���ڲ����ؾ��⹦�ܡ�

������������Ϳ���ΪӦ�ô����ⲿ DNS ��¼��������ӳ�䵽�κλ����� Docker swarm �ڵ㡣�����赣���������������У���Ϊ��������·��·�ɹ��ܣ���Ⱥ�е����нڵ����һ�����塣

    #Create a service with two replicas and export port 8000 on the cluster
    $ docker service create --name app --replicas 2 --network appnet --publish 8000:80 nginx

![����·��](./images/routing-mesh.png)

��ͼ�������·�ɵĹ���ԭ�������˵����

* �������������ӽڵ�ķ��񣬲�����˿�ӳ�䵽�ⲿ�˿� `8000`��
* ����·���ڼ�Ⱥ�е�ÿ�������Ϲ����˿� `8000`��
* ����`Ӧ��`�������ɴ��κ��������롣����������£��ⲿ LB ����ͨ������ӽڵ�Ϳɽ��������͵�������
* �ں˵� IPVS ���ؾ�������`���` overlay �����ϵ������ض�������״�����õķ���ӽڵ㡣

## HTTP ����·��

swarm mode ����·�ɷǳ��ʺϴ����·�ɡ���ʹ�÷�����ѷ����˿�·�ɵ����񡣵��ǣ����ϣ������������������·�ɵ�����Ӧ����ô�죿HTTP ����·�� (HRM) ��һ���¹��ܣ���������Ӧ�ò� (L7)�����÷����֡�HRM ͨ����� HTTP ��ͷ����Ӧ�ò㹦�ܶ� swarm mode ����·�ɽ�������չ��HRM �� swarm mode ����·�����߽��ʹ�ÿ�ʵ��������Ƚ��ķ��񽻸������ HRM ��ͨ�����ݸ������ DNS ���ʹÿ�����񶼿��Ա����ʡ����ŷ���ĺ�����չ�Լ�����ӽڵ����ӣ�����Ҳ��ʹ����ѯ���ؾ��⡣

HRM ͨ��ʹ�� `HTTP/1.1` ��ͷ�ֶζ��幤����ÿ�� `HTTP/1.1` TCP ���󶼰��� `Host:` ��ͷ������ʹ�� `curl`�鿴 HTTP �����ͷ��

    $ curl -v docker.com
    * Rebuilt URL to: docker.com/
    *   Trying 52.20.149.52...
    * Connected to docker.com (52.20.149.52) port 80 (#0)
    > GET / HTTP/1.1
    > Host: docker.com
    > User-Agent: curl/7.49.1
    > Accept:*/*

�� HRM �� HTTP �������ʹ��ʱ����Ҫ����ʹ�� swarm mode ����·�ɺ� HRM����ʹ�� `com.docker.ucp.mesh.http` ��Ǵ�������ʱ��HRM ���ý�����£��Խ����а��� `Host:` ��ͷ���� `com.docker.ucp.mesh.http` �����ָ������ HTTP ����·�ɵ��½������ VIP������ HRM �Ƿ��񣬿�ʹ�����õ��ѷ����˿��ڼ�Ⱥ�е��κνڵ��Ϸ��� HRM��

����ͼ����ʾ�� swarm mode ����·�ɺ� HRM ��ͬʹ��ʱ�Ĺ���ԭ��ĸ߼���ͼ��

![HRM �߼�](./images/routing-mesh-hrm.png)

* �������ⲿ���ؾ��������� swarm mode ����·�ɡ�
* HRM ��������Ϊ�����˿� 80 �� 8443������κζ� UCP ��Ⱥ�Ķ˿� 80 �� 8443 �����󶼻����� HRM ����
* �������ӵ������ÿ�ʵ�֡�������������·�ɡ�������ķ��񶼿�ʹ�� HRM ������ HTTP `Host:` ��ͷ·��������

��ϸ�������ͻ��˽� HRM �Ĺ������̡���ͼ����һͼ���б�ʾ�Ĺ���ԭ������˸���ϸ��˵����

![HRM ��ϸ˵��](./images/hrm-up-close.png)

* ��������`���`�����ϵ� swarm mode ����·������ HRM ������ѷ����˿ڡ�
* ��������ʱ������ swarm mode ����·�� (L4) �������Ƿ��� VIP��
* HRM ���� TCP ���ݰ������ HTTP ��ͷ��
    * �������� `com.docker.ucp.mesh.http` ��ǵķ������鿴�����Ƿ��� HTTP `Host:` ��ͷƥ�䡣
    * ��� `Host:` ��ͷ�ͷ�����ƥ�䣬�ͻ�ʹ�� swarm mode ����·�� (L4) ������·�ɵ������ VIP��
* ��������������ӽڵ㣬��ô��ʹ���ڲ� L4 ����·��ͨ����ѯ��ÿ���ӽڵ��������и��ؾ��⡣

### HRM �� Swarm Mode ����·�ɵĲ���

HRM �� swarm mode ����·�ɵ���Ҫ�������� HRM ���ܹ���Ӧ�ò��Ϲ� HTTP ����ʹ�ã��� swarm mode ����·���ڽϿ��µĴ�����Ϲ�����

����Ӧ��������ʹ����������·�ɡ����Ӧ����Ҫ������������Ϊ HTTP ������ô HRM �ͷǳ��ʺϡ������Ҫ����˫�� TLS ��֤�ſɷ��ʺ��Ӧ�ã���ôʹ�ô������ܻ�����ʺϡ�

ʹ�� HRM ����һ���������ڽ�����ٵ����þͿɽ�����·�ɵ�����ͨ�����ڷ��������ñ��ʱ����һ�� DNS ��¼�����ʹ����ͨ��� DNS ��¼����ô�����÷�����֮�⣬����������������á��ںܶ���֯�У�ͨ�������ƶԸ��ؾ������� DNS ���з��ʡ������ͨ�������ǾͿɿ��ƶ�Ӧ�õ����󣬿�����Ա���ܹ�����ѭ�����ʸ��ġ����� swarm mode ����·�ɣ�������������ǰ�˸��ؾ����������������͵�������ѷ����˿ڡ�

�����ͼ����ʾ��ʹ��ͨ��� DNS ��ʾ����

![HRM ͨ��� DNS](./images/ucp-hrm-wildcard-dns.png)

### ���� HRM

�ɴ� UCP Web ����̨���� HTTP ����·�ɡ�Ҫ��������

1. ��¼ UCP Web ����̨��
2. �����**����Ա����** > **����·��**��
3. ѡ��**���� HTTP ����·��**��
4. ���� HRM Ҫ�����Ķ˿ڣ�Ĭ��ֵΪ 80 �� 8443��HTTPS �˿�Ĭ��Ϊ 8443���������Ͳ����Ĭ�� UCP ����˿� (443) ������š�

![UCP HRM DNS](./images/ucp-hrm-enable.png)

���ú�UCP ���� swarm ��Ⱥ�ϴ������������� HTTP `Host:` ��ͷ������·�ɵ�ָ�������������� HRM ������ *swarm mode* ����UCP ��Ⱥ�е�ÿ���ڵ㶼��ͨ���������Զ˿� `80` �� `8443` ��������������·�ɵ� HRM��HRM �����ڼ�Ⱥ��Χ�ڹ����˿� 80 �� 8443���κ�ͨ���˿� 80 �� 8443 ���͵ĶԼ�Ⱥ�����󶼻ᷢ�͵� HRM��

### ����ͷ��ʿ���

HTTP ����·��ʹ��һ������ overlay ���������˷������ͨ�š�Ĭ������»ᴴ����Ϊ `ucp-hrm` �ĵ������磬���ʿ��Ʊ��Ϊ `ucp-hrm`��Ҫ���������ӷ�����Ҫ�߱�����Ա����ķ���Ȩ�ޣ����û��������ھ��� `ucp-hrm` ����Ȩ�޵��顣

��Ĭ�������޷���ʹ�� HTTP ����·�ɵĸ���������и��룬��Ϊ���������� `ucp-hrm` ���硣

Ҫ�Է���ʵʩ���룬���������� HTTP ����·��ǰʹ�� `com.docker.ucp.mesh.http` ��Ǵ���һ������ overlay ���硣HRM ���ú�������·�ɵ����ӵ���Щ��������з��񣬵��ǲ�ͬ�����ϵķ�����ֱ��ͨ�š�Ҫʹ HRM ���µ������Ͽ��ã�Ψһ�����ǽ��ò��������� HRM��

����Ϊ�������� `com.docker.mesh.http` ��ǵ� overlay �����ʾ�����Թ���Ա�û�����й���ԱȨ�޵��û�ʹ�� UCP �ͻ���֤���ʱ���������������

    docker network create -d overlay --label com.docker.ucp.mesh.http=true new-hrm-network

Ҳ��ʹ�� UCP UI �ﵽ��ͬ��Ŀ�ģ��������ڴ�������ʱѡ��**���û�����������·��**��

![UCP ����������·��](./images/ucp-ui-enable-hostname-routing.png)

### HRM Ҫ��

Ҫʹ�� HRM��������Ҫ��������Ҫ��

1. ����������ӵ����� `com.docker.ucp.mesh.http` ��ǵ�����
2. ������뷢��һ�������˿�
3. ����������һ����������ǰ׺ `com.docker.ucp.mesh.http` �ı�ǣ���ָ��Ҫ·�ɵĶ˿�

### ��� HRM ���� DNS

�ò��ֽ������Ϊʹ�� HRM �ķ������� DNS��Ҫʹ�� HRM������� DNS ��¼��Ҫָ�� UCP ��Ⱥ������ swarm mode ����·���ṩ������ԣ���ͨ�����ֲ�ͬ�ķ�ʽ���÷���� DNS ��¼��

���������Ҫ���Ա��������ʲſɽ������͵� `foo.example.com`����ô����ͨ�������κ�һ�ַ�ʽ���÷���� DNS ��¼��

1. �� DNS ����Ϊָ�� UCP ��Ⱥ�ϵ��κ�һ���ڵ㡣���� `foo.example.com` ���������󶼽�ͨ���ýڵ�·�ɵ� HRM��
2. ������ѯ DNS ��ָ�� UCP ��Ⱥ�ϵĶ���ڵ㡣�κ��յ����� `foo.example.com` ������Ľڵ㶼��ͨ�� HRM ��·�ɡ�
3. **���**���������Ϊʵ��**�߿�����**������ UCP ��Ⱥ��ǰ�������ⲿ HA ���ؾ�������ʹ���ⲿ HA ���ؾ�����ʱ��Ҫ�μ�����ע�����
    * �� `foo.example.com` �� DNS ��¼����Ϊָ���ⲿ���ؾ�������
    * �ⲿ���ؾ�����Ӧָ��פ���ڲ�ͬ�Ŀ����������еĶ�� UCP �ڵ㣬����ǿ�ɻָ��ԡ�
    * �����ⲿ���ؾ��������� HRM ����������õ��ѷ����˿�ִ�� TCP ����״����飬�Ա�ͨ������״�����õ� UCP �ڵ�·��������

![UCP HRM DNS](./images/ucp-hrm-dns.png)

Ӧ��ʹ����Щ�ڵ���·������������ڵ㻹�ǹ����ڵ㣿��Դ�����Ļش��кü��֡�

1. ���ڹ�ģ��С�Ĳ��𣬿�ͨ������ڵ���·����������Ϊ����ڵ�ͨ��Ϊ��̬���ʡ�
    * **����**������ͨ�����ᣨ������������ IP ��֮�䣩�����䶯����������ʹ���ؾ�����ָ����ͬ�Ľڵ㡣
    * **ȱ��**�����Ǹ���·�ɿ���ƽ�����������Ӧ�������϶࣬�������ַ������ܻ�ʹ��Щ�ڵ��ϵ��������ͣ��Ӷ�����Ⱥ��������Ӱ�졣
2. ͨ�������ڵ�·������
    * **����**�����ǲ�����������Ⱥ����˶������翪�����١�
    * **ȱ��**���������ڡ�cattle���ڵ㡣������ؾ�����ָ�����ڵ㣬Χ�����ٺ͹����ڵ㹹���Զ���ʱ��Ҫ������һ�㡣

����ǰ�˸��ؾ�����������ָ���������͵�ʵ��������Ҫȷ��ʵ�������㹻���������ӡ�

## HRM �÷�

�������Ѿ��˽��� HRM �Ĺ���ԭ���������ص�Ҫ�󣬱����ֽ��������� HTTP ·�ɡ���־��¼����غʹӽڵ�� HRM �﷨��

### HTTP ·��

����������һ����ǣ����Ǽ��� `com.docker.ucp.mesh.http` ��ͷ�����������Ҫ��������˿ڣ���ô����ʹ�ö����ǣ����磬`com.docker.ucp.mesh.http.80` �� `com.docker.ucp.mesh.http.443`��`80` �� `443` ����ͨ���˿ں����� HRM ��ǡ�������ʹ���κ�ֵ��ֻҪȷ�����Ǳ˴˲�ͬ���������Ը������Ǽ��ɡ�

���ӵ� HRM ʹ�õķ���ı�Ǽ������� `com.docker.ucp.mesh.http` ��ͷ�����磬`com.docker.ucp.mesh.http.80` �� `com.docker.ucp.mesh.http.443`��

��ǵ�ֵ���Զ��ŷָ��ļ�/ֵ�ԣ��ԵȺŷָ����б�����ʹ�����¶ԣ�

* `external_route` **�����裩** �� Ҫ·�ɵ��÷�����ⲿ URL��ʾ����`[http://myapp.example.com](http://myapp.example.com "http://myapp.example.com")` �� `[sni://myapp.example.com](sni://myapp.example.com "sni://myapp.example.com")`
* `internal_port` �� ���ڷ�����ڲ��˿ڡ�������񷢲�����˿ڣ���Ϊ**����**��ʾ����`80` �� `8443`
* `sticky_sessions` �� ������ڣ�ʹ�ø������� cookie �����û�·�ɵ��˷������ͬ������񡣽����ڱ��ĵ������ *Sticky Sessions* ���ֶ���������ϸ˵����
* `redirect` �� ������ڣ�ִ�е�ָ�� URL ���ض�������ı��ĵ������*�ض���*���֡�

### ��־��¼

��ͨ��ִ�����²�������¼���� HRM ��������

1. �� UCP UI �У�ת��**����Ա����** -> **��־**��

2. ��**��־���ؼ���**����Ϊ**����**��

    ![UCP HRM ������־��¼](./images/ucp-debug-logging.png)

3. ���� HRM ��������ʹ���κο��õ� [Docker ��־��¼��������](https://docs.docker.com/engine/admin/logging/overview/)��������ʹ��`ϵͳ��־`���������ʾ����

`docker service update --log-driver=syslog --log-opt syslog-address=udp://<ip_address>:514 ucp-hrm`

### ���

Ҫ��ǰ�˸��ؾ�������� HRM��ʹ�� TCP ����״����齫���ؾ���������Ϊ��ؼ�Ⱥ�ϵ��ѹ����� HRM �˿ڡ������ HRM ����Ϊ����Ĭ�϶˿� `80` �� `8443`����ôǰ�˸��ؾ���������Ҫ������е����нڵ�ִ�� TCP ����״����鼴�ɡ�

### HRM HA ע������

�����ֽ����� HRM �ļ���ʹ��ע�����

���ʹ��ճ�ԻỰ���ܣ��޷������дӽڵ�乲�� HRM ����ʵ�ֳ־û��� stick table �� ��ˣ�ֻ������ HRM ��һ���ӽڵ㡣���仰˵�����ʹ�û��� cookie �ĳ־û���HRM ֻ����Ϊһ���ӽڵ����С�

�����ʹ�� HTTP ·�ɣ���ճ�ԻỰ���� HTTPS ·�ɣ���ô���� HA Ŀ�ģ��ɽ� HRM ��չ������ӽڵ㡣

�������ʹ�û��� cookie �ĳ־û����ɽ� HRM ������չ������ӽڵ㡣���磬Ҫʹ�� 3 ���ӽڵ㣺

    docker service update --replicas 3 ucp-hrm

## HRM �÷�ʾ��

�����ֽ�����ʹ�����п��õ� HRM ����ģ�͵��������͵�Ӧ�ã�

* HTTP ·��
* ճ�ԻỰ
* HTTPS
* ����˿�
* �ض���

Ҫ��ϰ��Щչʾ�����ֺ͸��ؾ����ʾ����������������������

1. ��װ�˾��� UCP �ͻ���֤����� Docker �ͻ��ˣ������������ UCP ��Ⱥͨ�š�
2. ָ���ؾ������� DNS λ�� UCP ��Ⱥǰ�ˡ�����޸��ؾ������ɹ�ʹ�ã���ô�뽫���������ļ��е���Ŀָ�� UCP ��Ⱥ�е����������Ҫֱ�����ӵ� UCP ��Ⱥ�е���������ʹ���ѷ����� HRM �˿ڣ�Ĭ�� 80 �� 8443���������ǡ�

> **ע��**���� [GitHub](https://github.com/ahromis/spring-session-docker-demo)���ҵ�ʾ��Ӧ�õľ���ֿ⡣

### HTTP ·��ʾ��

����չʾ Docker EE �еķ����ֺ͸��ؾ����ʾ��Ӧ�á�

Ҫ����Ӧ��Ӧ��ջ���ڼ����� UCP �ͻ���֤���������������������

    $ wget https://raw.githubusercontent.com/ahromis/spring-session-docker-demo/master/ucp-demo/docker-compose.hrm.http.yml
    $ DOMAIN=<domain-to-route> docker stack deploy -c docker-compose.hrm.http.yml hrm-http-example

Ȼ�����ʾ��Ӧ�ã���ַΪ `http://<domain-to-route>`����ʹ��**�û�����**`user` ��**���룺**`password` ��¼��

��������븴��/ճ���� UCP UI����Ϊ Compose �ļ������ݣ�

    version: ��3.1"

    services:
        redis:
          image: redis:3.0.7
          hostname: redis
          networks:
            - back-end
          deploy:
            mode: replicated
            replicas:1
        session-example:
          image: ahromis/session-example:0.1
          ports:
            - 8080
          networks:
            - back-end
            - ucp-hrm
          deploy:
            mode: replicated
            replicas:5
            labels:
              - com.docker.ucp.mesh.http.8080=external_route=http://${DOMAIN},internal_port=8080

    networks:
      back-end:
        driver: overlay
      ucp-hrm:
        external:
          name: ucp-hrm

��ͨ�� UCP UI ת��**��Դ** -> **Ӧ��ջ��Ӧ��** -> **����**���в��𡣶�Ӧ��ջ����������Ȼ��������� compose �ļ�������ճ���������ı��ֶΡ�ͨ�� UI ����ʱ��������滻 `${DOMAIN}` ������

![UCP UI Ӧ��ջ����](./images/ucp-ui-stack-deploy.png)

HRM һ�������½����񣬾ͻ��� UCP �н����г���ת��**����Ա����** -> **����·��**����Ӧ��Ӧ��**�����õ�����**���г���

![HRM �����õ�����](./images/ucp-hrm-configured-hosts.png)

#### HRM ���������

HRM ÿ 30 �����һ����ѯ�����Ӧ��һ������HRM �ͻ���·��������ѯ���� `http://<domain-to-route>` ��������

���� compose �ļ�ʱ����������������

1. ��������������ǰ�� Spring Boot Ӧ�úʹ洢�Ự���ݵ� Redis ����
2. �������ض��ڴ��ض�Ӧ��Ӧ��ջ���� overlay ���磬����Ϊ `<stack-name>_backend`��
3. �� Redis ���񽫴��� `redis` �� DNS ��¼���� `backend` �����ϣ����� DNS ��¼ָ�� Redis ������ IP ��ַ��
    * ǰ��Ӧ�õ����������ڷ��� Redis ʱΪÿ��Ӧ��ջ���ġ�����ÿ����������Ӧ�������У���������Ϊ `spring.redis.host=redis`��
4. Ȼ��ǰ�˷������Ƶ� DNS ��Ŀ `session-example` ���� `ucp-hrm` ������ע�ᡣ
5. ǰ�˷��񽫴����������ӵ� `ucp-hrm` ����
6. �������ķ������������״̬Ϊ 5 ���ӽڵ㣬��˽��ᴴ�� 5 ���ӽڵ�����
7. HRM ÿ 30 ��� Docker �¼�������ѯ����������ʰȡ�½������ϵ� `com.docker.ucp.mesh.http.8080` ��ǡ�
8. HRM ������һ����Ŀ��ʹ���е� 5 ��ǰ�˴ӽڵ����ΪӦ��ջ��������ݵ� `$DOMAIN` �����������и��ؾ��⡣
9. ͨ���� Web �������ˢ�� `[http://$DOMAIN](http://$DOMAIN "http://$DOMAIN")`��ÿ�������ͺ󶼻���ʾ�µ�������������������ǰ�˷���ӽڵ����и��ؾ��⡣
10. �������ӵ�¼��ƾ����**�û�����**`user`��**���룺**`password` ��¼��

### HRM ճ�ԻỰʾ��

HTTP ����·���ܹ����������� cookie ·�ɵ��ض��ĺ�˷������磬���Ӧ��ʹ������Ϊ `JSESSIONID` �� cookie ��Ϊ�Ự cookie��������ͨ���� `sticky_sessions=JSESSIONID` ���ݵ� HRM ������־ô洢���ض�����ӽڵ���������ӡ��� HRM �У�ճ������ͨ��ʹ�� stick table ����ɣ����У�HRM �����ֲ�ʹ��Ӧ�ûỰ cookie ���־ô洢���ض���˴ӽڵ�����ӡ�

Ϊ����Ҫʹ�û��� cookie �ĳ־û��������Լ��ٸ��ؾ������ϵĸ��ء����ؾ�����ʰȡ��˳��е��ض�ʵ�����������ӣ����Ǳ�������������ʱ����·�ɡ�����һ��Ӧ�ó���������Թ������𡣵����µ�Ӧ�÷��������븺�ؾ���������ʱ����ʵ�����������ȺЧӦ���෴�����ŻỰ������������ؽ�����ʵ��������ת��Ϊ���ؾ��⡣

������ԣ�ճ�ԻỰ���ʺ�����߻������ܺͼ���ϵͳ�ض�����ĸ��ء������ΪӦ��δʹ�÷ֲ�ʽ�洢����Ҫÿ�ζ�����ͬһ��ˣ���ô�� Swarm Mode ���µ�������ʱ��������δ�������������⡣�������ʹ��Ӧ�û��� cookie �ĳ־û�ʱ�μ���һ�㡣

> **ע��**ʹ�� HTTPS ʱճ�ԻỰ�������ã���Ϊ cookie ��ֵ���� HTTP ��ͷ�м��ܡ�

ҪΪճ�ԻỰ����ʾ��Ӧ��Ӧ��ջ���ڼ��� UCP �ͻ���֤���������������������

    wget https://raw.githubusercontent.com/ahromis/spring-session-docker-demo/master/ucp-demo/docker-compose.hrm.sticky.yml
    DOMAIN=<domain-to-route> docker stack deploy -c docker-compose.hrm.http.yml hrm-sticky-example

����ʾ��Ӧ�ã���ַΪ `http://<domain-to-route>`����ʹ��**�û�����**`user`��**���룺**`password` ��¼��

������븴��/ճ���� UCP UI �У���Ϊ compose �ļ������ݣ�

    version: "3.1"

    services:
        redis:
          image: redis:3.0.7
          hostname: redis
          networks:
            - back-end
          deploy:
            mode: replicated
            replicas:1
        session-example:
          image: ahromis/session-example:0.1
          ports:
            - 8080
          networks:
            - back-end
            - ucp-hrm
          deploy:
            mode: replicated
            replicas:5
            labels:
              - com.docker.ucp.mesh.http.8080=external_route=http://${DOMAIN},internal_port=8080,sticky_sessions=SESSION

    networks:
      back-end:
        driver: overlay
      ucp-hrm:
        external:
          name: ucp-hrm

ͨ�� UI ����ʱ��������滻 `${DOMAIN}` ������

#### HRM ճ�ԻỰ���

���ʺ͵�¼Ӧ��ʱ,��Ӧ��������ͼ���Ƶ�ҳ�档

![�Ựʾ����Ļ��ͼ](./images/session-example-screenshot.png)

���� HTTP ·��ʾ����ͬ�����Ǿ��и��ӵ� `sticky_sessions=SESSION` ��ֵ��Ŀ��

�� `sticky_sessions` ��ӵ� `com.docker.ucp.mesh.http` ����ʲô���ã�

1. HRM ������һ����Ŀ��ʹ���� 5 ��ǰ�˴ӽڵ�� IP ����ӵ������С���������֮�⣬���������Ϊ�־û��Ļ����ĻỰ cookie �����ơ�
2. �� Web ������м��� `[http://$DOMAIN](http://$DOMAIN "http://$DOMAIN")`����ʹ��**�û�����**`user`��**���룺**`password` ��¼������ճ�ԻỰ��ˢ��ҳ��Ӧ��ʾͬһ��˷������������ʵ�������ӽ����� `SESSION` cookie ��ֵ�־ô洢��

����ʾӦ��ʹ�� Redis ��Ϊ�Ự���ݵķֲ�ʽ�洢������ͨ���� UCP UI �д򿪵� Redis �����Ŀ���̨���鿴�洢�� Redis �е� `SESSION` cookie��

1. ��¼ UCP UI��
2. ����ർ�������е���**Ӧ��ջ��Ӧ��**��
3. ���б���ѡ��Ӧ��ջ��
4. ���б���ѡ�� redis ����
5. ����������**����**��
6. ѡ�� Redis ������
7. ����������**����̨**��
8. ʹ�� `sh` �����ӵ�����̨Ӧ��ջ��
9. �ڿ���̨������ `redis-cli keys "*"`��

![�Ự ID Redis ��Ļ��ͼ](./images/session-id-redis.png)

### HRM HTTPS ʾ��

HTTP ����·��֧��ʹ�� HTTPS ��·�ɡ�ʹ�ó���*����������ָʾ*�� HTTPS ���ܣ�HRM ������ֹ HTTPS ���Ӿ��ܹ�������·�ɵ������ˡ�SNI �� TLS Э���һ����չ�����У��ͻ��������ֹ��̿�ʼ��ʱ���ָʾ���������ӵ�����������

Ҫʹ�� HTTPS ֧�֣������� HTTP ����·���ṩ�����֤�顣�෴����˷������ֱ�Ӵ��� HTTPS ���ӡ�����������ķ���ſ�ʹ�� SNI Э����ָʾ�����ַ�ʽ���� HTTPS �������ڵ�Ӧ�÷���������ֹ��������������һֱ����Ӧ�á����ǣ���Ҳ��ζ��Ӧ�ñ������ TLS ֤�顣ͨ������ Docker ������Ϣ���������ɰ�ȫ�ع���Ӧ�÷�������֤�顣

�� HRM �� HTTPS ����ʹ��ʱ�����ظ�ʹ�������Խ�������Э���µ� TLS ���ӵĿ�����ͨ��ʹ�� SSL �Ự ID ���ظ�ʹ�� HTTPS ���ӣ����ǽ��־ô洢����������ֱ����Ҫ���½������ӣ�����Ӧ�÷��������ӳ�ʱ����

�������ͨ��ʱ����һ�����õ�������Ϣ��֤�顣�ڲ���ʾ��Ӧ��ǰ�����ȴ��� Java ��Կ�洢��

> **��ʾ��**������һЩ���õ�����ɴ��������� Java Ӧ�õ���Կ�洢��

    # create PKCS#12 file format
    $ openssl pkcs12 -export -out keystore.pkcs12 -in fullchain.pem -inkey privkey.pem
    # convert PKCS file into Java keystore format
    $ docker run --rm -it -v $(pwd):/tmp -w /tmp java:8 \
        keytool -importkeystore -srckeystore keystore.pkcs12 -srcstoretype PKCS12 -destkeystore keystore.jks

�����Ѿ��������� Java ��Կ�洢���������͸ý���ת��Ϊ Docker ������Ϣ��������Ӧ�þͿ��԰�ȫ��ʹ�����ˡ�

    $ docker secret create session-example_keystore.jks_v1 keystore.jks
    $ echo "<your-key-store-password>" | docker secret create session-example_keystore-password.txt_v1 -
    $ echo "<your-key-password>" | docker secret create session-example_key-password.txt_v1 -

��������������������Ϣ���ڼ�Ⱥ��Χ�ڵļ�ֵ�洢�м����ˡ�������Ϣ���þ�̬���ܷ������ܣ������ƶ�����Ҫ������Ϣ�Ľڵ�Ĺ�����ʹ�� TLS��ֻ����Ҫʹ��������Ϣ��Ӧ�òſɲ鿴������Ϣ��

> **��ʾ��**�й�ʹ�� Docker ������Ϣ�ĸ�����ϸ��Ϣ������ĺ��� [DDC ��ȫ�����ʵ��](https://success.docker.com/Architecture/Docker_Reference_Architecture%3A_Securing_Docker_EE_and_Security_Best_Practices#Secrets)�Ĳο��ܹ���

Ҫ��Ӹ��������ģ�����ʾ��Ӧ��ʹ�õ�Ӧ�����ã�

    spring.redis.host=redis
    spring.redis.port=6379
    server.port=8443
    server.ssl.key-store=file:/run/secrets/keystore.jks
    server.ssl.key-store-password=${KEY_STORE_PASSWORD}
    server.ssl.key-password=${KEY_PASSWORD}

������������ `ENTRYPOINT` �ű���������ȡ������Ϣ��Ȼ���乫���� Spring Boot�����ڸ�Ӧ�õ� [GitHub ����ֿ�](https://github.com/ahromis/spring-session-docker-demo)���ҵ�������Ϣ��

����֤���Ѱ�ȫ�������洢�� Docker KV �洢�У��������ô����ܹ�ʹ�����ǵķ����ˣ�

    wget https://raw.githubusercontent.com/ahromis/spring-session-docker-demo/master/ucp-demo/docker-compose.hrm.ssl.yml
    DOMAIN=<domain-to-route> docker stack deploy -c docker-compose.hrm.http.yml hrm-sticky-example

����ʾ��Ӧ�ã���ַΪ `https://<domain-to-route>`�����δʹ������ UCP ��Ⱥǰ�˵ĸ��ؾ���������ô��ʹ�� `https://<domain-to-route>:8443`��Ȼ��ʹ��**�û�����**`user`��**���룺**`password` ��¼��

������ UI ���и���/ճ���������������� compose �ļ�����ǵ��滻 `${DOMAIN}` ��������

    version: "3.1"

    services:
        redis:
          image: redis:3.0.7
          hostname: redis
          networks:
            - back-end
          deploy:
            mode: replicated
            replicas:1
        session-example:
          image: ahromis/session-example:0.1
          ports:
            - 8443
          environment:
            - SPRING_PROFILES_ACTIVE=https
          networks:
            - back-end
            - ucp-hrm
          deploy:
            mode: replicated
            replicas:5
            labels:
              - com.docker.ucp.mesh.http.1=external_route=http://${DOMAIN},redirect=https://${DOMAIN}
              - com.docker.ucp.mesh.http.8443=external_route=sni://${DOMAIN},internal_port=8443
          secrets:
            - source: session-example_keystore.jks_v1
              target: keystore.jks
              mode:0400
            - source: session-example_keystore-password.txt_v1
              target: keystore-password.txt
              mode:0400
            - source: session-example_key-password.txt_v1
              target: key-password.txt
              mode:0400

    networks:
      back-end:
        driver: overlay
      ucp-hrm:
        external:
          name: ucp-hrm

    secrets:
        session-example_keystore.jks_v1:
            external: true
        session-example_keystore-password.txt_v1:
            external: true
        session-example_key-password.txt_v1:
            external: true

�����ʾ��Ҳչʾ�����ͨ��ʹ�� `redirect` ��Կ�Դ���� HTTP �� HTTPS ���ض���

ֵ��һ���һ���ǣ����ǳ������ظ�ʹ�ã���ʹ������Ӧ��Ҳ����ˡ�ֻ�輸���򵥲���Ϳ�ִ�� Modernize Traditional Applications (MTA)������ֻ������������ø��ľͿɶ�β����Ӧ��Ӧ��ջ��

### HRM ����˿�ʾ��

��ʱ������������Ķ���˿ڡ����� HRM���ɶ���·�ɵ�ÿ�������˿ڡ�

�����Ǿ��ж�������˿ڵķ����ʾ����

    $ docker service create \
      -l com.docker.ucp.mesh.http.8000=external_route=http://site1.example.com,internal_port=8000 \
      -l com.docker.ucp.mesh.http.8001=external_route=http://site2.example.com,internal_port=8001 \
      -p 8000 \
      -p 8001 \
      --network ucp-hrm \
      --replicas 3 \
      --name twosite ahromis/nginx-twosite:latest

�ڱ�ʾ���У�������һ�� Nginx ������������������ͬ�˿ڵ� Web ��Ŀ¼��HRM �������յ��� HTTP `Host:` ��ͷ�����ؽ�����·�ɵ�ÿ��վ�㡣

### HRM �ض���ʾ��

��Ҫǿ��ʹ�������Ӷ���Ϊ��ȫ����ʱ���ɴ� HTTP �ض��� HTTPS�����ض���ѡ��ָʾ�Ը�·�ɵ���������Ӧʹ�� HTTP �ض����ض�������������

�˹��ܵ�һ����;�����ڽ�ʹ�� HTTPS ���������ķ���HTTP �������ض��� HTTPS��������÷���λ�� example.com �ϣ���ô����ʹ�������������ɣ�

    com.docker.ucp.mesh.http.1=external_route=http://example.com,redirect=https://example.com
    com.docker.ucp.mesh.http.2=external_route=sni://example.com

����һ����;������ֻ���յ������ϵ���������������������ض������ķ������磬һ�������������վ���ܻ�ʹ�øù��ܡ�����ı�ǿ�Ϊ `new.example.com` �� `old.example.com` ʵ�ִ�Ч����

    com.docker.ucp.mesh.http.1=external_route=http://old.example.com,redirect=http://new.example.com
    com.docker.ucp.mesh.http.2=external_route=http://new.example.com

������ʹ�ò�ͬ��ʾ��Ӧ�ý���վ�ض��򵽲�ͬ�����ʾ����

    $ docker service create \
      -l com.docker.ucp.mesh.http.1=external_route=http://oldsite.example.com,redirect=http://foo.example.com \
      -l com.docker.ucp.mesh.http.8080=external_route=http://foo.example.com,internal_port=8080 \
      --replicas 3 \
      --name lbinfo \
      ahromis/lbinfo:latest

## �� Swarm Mode ����

HRM �� swarm mode ����·�ɽ�֧��ʹ�á����񡱲����Ӧ�á����ڷ� swarm mode ���������磬�� 1.12 �汾֮ǰ�� Docker ���������е�������δʹ�÷������Ӧ�ã����磬ʹ�� `docker run`��������ʹ�� [interlock](https://github.com/ehazlett/interlock) �� NGINX��

Interlock ���������ġ��¼������Ĺ��ߣ������ӵ� UCP �������������¼�������������£��¼�ָ�������������������Interlock ���������Щ�������е��ض�Ԫ���ݣ����磬��������Ϊ�������õı�ǡ�Ȼ����ʹ��Ԫ���ݽ���Щ����ע�ᵽ���ؾ����� (NGINX) ��ȡ��ע�ᡣ���ؾ�����ʹ�ø��º�ĺ�����ý���������������״�����õ�������Interlock �͸��ؾ���������������״̬�ģ���ˣ��ɿ����ڵ�����ǽ��к�����չ����Ϊ�����Ѳ����Ӧ���ṩ�߶ȿ��õĸ��ؾ������

Ҫʹ�� Interlock �� NGINX��������Ҫ��������Ҫ��

**I. ��Ҫ��һ������ UCP �����ڵ��ϲ��� Interlock �� NGINX��**

���� Interlock �� NGINX ��򵥵ķ������� UCP �Ż���ʹ�� Docker Compose��

1. ��¼ UCP Web ����̨��
2. �ڶ�����ת��**��Դ**ѡ���
3. ����ര���У�ת��**Ӧ��**��������**���� compose.yml** ��ť��
4. ���� `interlock` ��Ϊ**Ӧ������**��

5. ���� `compose.yml` �ļ��������������ʾ�� `compose.yml` �ļ����ɰ�����Ҫ���� Interlock �� NGINX config������ [GitHub](https://github.com/ehazlett/interlock/tree/master/docs) ���ҵ������ĵ���

        interlock:
        	    image: ehazlett/interlock:1.3.0
        	    command:-D run
        	    tty: true
        	    ports:
        	        - 8080
        	    environment:
        	        constraint:node==${UCP_NODE_NAME}:
        	        INTERLOCK_CONFIG:|
        	            ListenAddr = ":8080"
        	            DockerURL = "tcp://${UCP_CONTROLLER_IP}:2376"
        	            TLSCACert = "/certs/ca.pem"
        	            TLSCert = "/certs/cert.pem"
        	            TLSKey = "/certs/key.pem"
        	            PollInterval = "10s"
        	            [[Extensions]]
        	            Name = "nginx"
        	            ConfigPath = "/etc/nginx/nginx.conf"
        	            PidPath = "/etc/nginx/nginx.pid"
        	            MaxConn = 1024
        	            Port = 80
        	    volumes:
        	        - ucp-node-certs:/certs
        	    restart: always


        	nginx:
        	    image: nginx:latest
        	    entrypoint: nginx
        	    command:-g "daemon off;" -c /etc/nginx/nginx.conf
        	    ports:
        	        - 80:80
        	    labels:
        	        - "interlock.ext.name=nginx"
        	    restart: always
        	    environment:
        	        constraint:node==${UCP_NODE_NAME}:

    > **ע��**���滻 `UCP_NODE_NAME` �� `UCP_CONTROLLER_IP`��`UCP_NODE_NAME` ��Ҫ���������� Interlock �� NGINX �Ľڵ�����ƣ���**��Դ**/**�ڵ�**��������ʾ����Ӧ�õ� DNS ������Ҫ�������ýڵ㡣`UCP_CONTROLLER_IP` ��һ������ UCP �������� IP �� DNS ���ơ�

6. ����**����**������ Interlock �� NGINX��

> **ע��**��ͨ���ظ������Ĳ��� 3-6 ������**Ӧ������**�� `UCP_NODE_NAME` ���ڶ���ڵ��ϲ��� Interlock �� NGINX����ʹ����������Щ�ڵ�ǰ�˷����ⲿ���ؾ����������磬ELB �� F5����ʵ�ָ߿����ԡ�Ȼ����Ҫ��Ӧ�õ� DNS ��¼ע�ᵽ�ⲿ���ؾ����� IP��

**II.�������뷢��һ�������˿ڡ�**

**III.��������ʹ�� Interlock [���](https://github.com/ehazlett/interlock/blob/master/docs/interlock_data.md)������**

���磬Ҫ���𹫿��˿� `8080` ��ͨ�� DNS ���� `demo.app.example.com` ���ܷ��ʵ��������밴������ķ�����������

    docker run --name demo -p 8080 --label interlock.hostname=demo --label interlock.domain=app.example.com ehazlett/docker-demo:dcus

ʹ����ȷ�ı�Ǻ��ѷ����˿����������󣬾Ϳ�ʹ������� DNS ���Ʒ�������

## �ܽ�

�� Docker �У���չ�ͷ��ַ���Ĺ��ܱ������κ�ʱ�򶼸��򵥡����ڷ����ֺ͸��ؾ��⹦�������õ� Docker �У�����ʦ���Խ�Լ���Դ�����Щ���͵�֧�ֹ��ܵ�ʱ�䣬�Ӷ������ྫ��������Ӧ���ϡ����贴���������õ� DNS �� API �Ϳ�ʵ�ַ����֣���Ϊ Docker ���Զ������������Ӧ����Ҫ��չ��Docker Ҳ���Զ�������ӵ����ؾ������ء�������Щ���ܣ���֯�����ڸ��̵�ʱ���ڽ����߶ȿ��õ�Ӧ�á�
