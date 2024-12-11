# S3 Replication by DataSycn



### 3rd party solution에서 Versioning 상태의 Source S3 Bucket의 Object를 Permanent delete시 Replication이 걸려 있는 Target S3 Bucket에는 해당 Object가 삭제 되지 않는 문제에 대한 문의 



---



1. Source Bucket에서 Replication 설정





```
#!/bin/bash

# S3 버킷 이름
source_bucket_name="s3-replicate-source-kiwony-20241209"
target_bucket_name="s3-replicate-target-kiwony-20241209"

while true
do
  # 현재 날짜와 시간을 YYYYMMDD-HH-MM-SS 형식으로 가져오기
  current_date=$(date +"%Y%m%d-%H-%M-%S")

  # 두 개의 파일 생성
  echo $current_date > "${current_date}-remain"
  echo $current_date > "${current_date}-deleted"

  # S3 버킷으로 파일 복사
  aws s3 cp "${current_date}-remain" s3://$source_bucket_name/
  aws s3 cp "${current_date}-deleted" s3://$source_bucket_name/

  # S3에 업로드된 -deleted 파일 삭제
  aws s3 rm "s3://$source_bucket_name/${current_date}-deleted"

  aws s3 ls "s3://$source_bucket_name/${current_date}-remain"
  aws s3 ls "s3://$target_bucket_name/${current_date}-remain"

  # 1분 대기
  sleep 60
done
```

dd