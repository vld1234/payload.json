# payload.json
Gitlab CI notifcation

###
.notification_template:
  variables:
    TEAMS_WEBHOOK_URL: "$TEAMS_WEBHOOK_URL"
  script:
    - apk add --no-cache curl bash jq
    - |
        cat << EOF > payload.json
        {}
        EOF
        curl -H "Content-Type:application/json" -d @payload.json "$TEAMS_WEBHOOK_URL"

teams_notification_when_pipeline_failed:
  stage: notify_results
  needs:
    - job: deploy_stage_aws
      optional: true
    - job: tag
      artifacts: true
      optional: true
  extends:
    - .pipeline_workflow
  rules:
    - if: $CI_COMMIT_BRANCH == "master"
      when: on_failure
    - when: never
  variables:
    STATUS: "failed"
    ICON: "❌"

teams_notification_when_pipeline_succeed:
  stage: notify_results
  needs:
    - job: deploy_stage_aws
      optional: true
    - job: tag
      artifacts: true
      optional: true
  extends:
    - .pipeline_workflow
  rules:
    - if: $CI_COMMIT_BRANCH == "master"
      when: on_success
    - when: never
  variables:
    STATUS: "success"
    ICON: "✅"
