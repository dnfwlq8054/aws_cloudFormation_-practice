cloudformation 을 중첩된 스택으로 사용하게 되면 다양한 문제에 직면할 수 있습니다.

`UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS` 상태에서 멈춰있을 경우, `UPDATE_ROLLBACK_FAILED` 상태에서 멈춰있을 경우 등...

이럴때 skip할 수 있는 방법이 있습니다.

`aws cloudformation continue-update-rollback --stack-name [stack name] --region ap-northeast-1 --resources-to-skip [resource name]`

위 방법으로 해당 스택 리소스에 대해서 스킵할 수 있습니다.

단, 특정 리소스 때문에 위 skip명령어가 안될 수 있습니다.

그럴땐 해당 특정 리소스만 골라서 적용시켜주면 됩니다.

`aws cloudformation continue-update-rollback --stack-name gopax_stack --region ap-northeast-1 --resources-to-skip gopax_lambda.gopax_create_lambda`


