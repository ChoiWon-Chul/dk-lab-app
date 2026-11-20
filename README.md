# dk-lab-app — Jenkins 입력용 소스 저장소

이 폴더의 **내용물만** `dk-lab-app` 공개 저장소 루트에 업로드합니다.

- `app/index.html` / `app/Dockerfile`: Nginx 정적 웹페이지
- `Jenkinsfile`: Checkout → Build → Smoke test → Registry Push → GitOps manifest commit
- Jenkinsfile 안의 `REPLACE_OWNER`를 본인의 GitHub 계정명으로 바꾼 후 커밋하세요.
- **절대 추가하지 말 것**: GitHub PAT, 비밀번호, 실서버 설정, 회사 내부자료, SSH 개인키
