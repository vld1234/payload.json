# payload.json
Gitlab CI notifcation

###
.notification_template:
  variables:
    TEAMS_WEBHOOK_URL: "$TEAMS_WEBHOOK_URL"
  script:
    - |
        cat << EOF > payload.json
        {}
        EOF
        curl -H "Content-Type:application/json" -d @payload.json "$TEAMS_WEBHOOK_URL"

teams_notification_when_pipeline_failed:
  stage: notify_on_teams
  needs:
    - job: latest_release-tag
  extends:
    - .notification_template
  rules:
    - when: on_failure
      if: "$CI_COMMIT_TAG"
      variables:
        STATUS: "failed"
        ICON: "❌"

teams_notification_when_pipeline_succeed:
  stage: notify_on_teams
  extends:
    - .notification_template
  rules:
    - when: on_success
      if: "$CI_COMMIT_TAG"
      variables:
        STATUS: "success"
        ICON: "✅"
