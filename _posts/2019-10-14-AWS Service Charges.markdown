---
layout: post
title:  "AWS Service Charges"
series:
date:   2019-10-14 11:00:00 +0900
abstract: ""
tags: [AWS]
image:
toc: true
categories: Tech
---



## AWS Service Charges

AWS 프리티어 사용자입니다.  
매달 몇백원씩 청구되길래 살펴봤어요. (아껴야 잘산다!)  

![aws console]({{ site.url }}/assets/article_images/2019-10-14-AWS Service Charges/2019-10-14-005.png)

청구되는 항목이 2개더군요.  
<br>
$0.05 per GB-Month of snapshot data stored - Asia Pacific (Seoul)  
번역 : GB-월당 저장된 스냅 샷 데이터 - 아시아 태평양 (서울)  

$0.005 per Elastic IP address not attached to a running instance per hour (prorated)  
번역 : 시간당 실행중인 인스턴스에 연결되지 않은 탄력적 IP 주소 (일할 계산)



![aws console]({{ site.url }}/assets/article_images/2019-10-14-AWS Service Charges/2019-10-14-001.png)

EC2 대시보드에서 스냅샷, 탄력적 IP 를 확인합니다.  
음~ 사용하는게 있군용!

## EBS

스냅샷 메뉴로 이동해서 삭제 해볼까요?  
![aws console]({{ site.url }}/assets/article_images/2019-10-14-AWS Service Charges/2019-10-14-002.png)

2개는 이미 사용중이니 삭제할 수 없다는 안내가 나오네요.  
어디에서 쓰고있나 확인해보니 `인스턴스 > 시작 템플릿` 에서 쓰고있군요.  
시작템플릿에서 쓰고있다면 `이미지 > AMI` 도 같이 지워야지요. (너와 나의 연!결!고!리! ^0^)  
확인해보고 지워도 되는 인스턴스이면 지워주세요.  

![aws console]({{ site.url }}/assets/article_images/2019-10-14-AWS Service Charges/2019-10-14-003.png)




## Elastic IP Addresses

연결된 인스턴스가 없는걸 확인했다면 주소 릴리즈 해주세요.  

![aws console]({{ site.url }}/assets/article_images/2019-10-14-AWS Service Charges/2019-10-14-004.png)


사용중인것은 지우지 않았습니다.  
필요없는 것들만 지워야겠지요.   

운영환경에서 인스턴스 몇백대 이럴텐데.. 요금누수 없이 운영하는게 쉽진 않을꺼 같다는 생각이 드네요.  
