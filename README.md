# auto_commit_bot
자동으로 잔디밭을 채우기 위한 레포지터리입니다.

테스트 용으로 만든 레포지터리이니 따라 하지 마세요

---

매일 자정 자동으로 20번의 커밋을 날리는 github action 코드입니다.
```yml
name: Daily Multi-Commit    # 표시되는 이름

on:
  schedule:
    - cron: '0 15 * * *'  # KST 00:00 = UTC 15:00
  workflow_dispatch:

permissions:
  contents: write    # 레포지터리 컨텐츠에 쓰기 권한을 부여 

jobs:
  multi_commit:
    runs-on: ubuntu-latest    # 리눅스 서버에서 실행

    # 현재 저장소를 github Action 실행 환경에 내려 받기 
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      # Git 사용자 정보 설정
      # 본인 계정으로 등록해야 커밋 기여도가 올라갈 것임
      - name: Set up Git config
        run: |
          git config --global user.name "${{ secrets.GIT_NAME }}"
          git config --global user.email "${{ secrets.GIT_EMAIL }}"

      # 20개의 커밋이 각각 기록됨 
      - name: Make 20 commits
        run: |
          for i in {1..20}
          do
            if [ $i -eq 1 ]; then
              echo "Commit #$i at $(date)" > log.txt
            else
              echo "Commit #$i at $(date)" >> log.txt
            fi

            git add log.txt
            git commit -m "Daily commit ($i/20): $(date)"
            sleep 1
          done

      # main 브랜치에 푸시
      - name: Push changes
        env:
          TOKEN: ${{ secrets.PERSONAL_TOKEN }}
        run: |
          git remote set-url origin https://x-access-token:${TOKEN}@github.com/${{ github.repository }}
          git push origin main
```
