# nuget.org Trusted Publishing для AIGuiders.AvaloniaEdit*

Пакеты: **`AIGuiders.AvaloniaEdit`**, **`AIGuiders.AvaloniaEdit.TextMate`**.  
Воркфлоу: [`.github/workflows/nuget-publish.yml`](../.github/workflows/nuget-publish.yml).

## 1. Учётка nuget.org (без секретов GitHub)

Как в [dotnet-mcp-templates](https://github.com/KarataevDmitry/dotnet-mcp-templates) и **AIGuiders.DotnetTools**: в workflow в шаге `NuGet/login@v1` задаётся **публичный** профиль (**`LonelySoul`**). Отдельные секреты репозитория для этого не нужны — OIDC Trusted Publishing всё делает после настройки политики.

## 2. Политика Trusted Publishing

1. Войти на [nuget.org](https://www.nuget.org/).
2. Аватар → **Trusted Publishing** → **Add new policy** (GitHub).
3. Заполнить (подставь фактический owner/repo форка):
   - **Repository owner:** `KarataevDmitry` (или организация, если репозиторий перенесён).
   - **Repository:** `AvaloniaEdit`.
   - **Workflow file:** `nuget-publish.yml` — **только имя файла**, без `.github/workflows/`.
   - **Environment:** пусто, если в YAML не используется `environment:`.

4. **Owner policy** на nuget.org выбрать ту учётку/организацию, которой принадлежат (или будут принадлежать) пакеты `AIGuiders.*`.

Политика может быть «временной» 7 дней для приватного репозитория, пока не произойдёт первый успешный push — см. [документацию](https://learn.microsoft.com/nuget/nuget-org/trusted-publishing#policies-pending-full-activation).

## 3. Первый выпуск и дальнейшие

1. Убедиться, что на nuget.org зарезервирован префикс **`AIGuiders.*`** (или отдельно созданы пакеты с этими ID под вашим владельцем — по правилам nuget.org на момент настройки).

**Через тег** (основной вариант): толкнуть тег имени **`v*`**, например `v12.0.1-cascade.1`; в NuGet попадёт версия **`12.0.1-cascade.1`** (буква `v` отбрасывается):

```bash
git tag v12.0.1-cascade.1
git push origin v12.0.1-cascade.1
```

**Вручную:** **Actions → Publish AIGuiders.AvaloniaEdit (nuget.org) → Run workflow** и поле **package version** (например `12.0.1-cascade.1`).

Временный API-ключ выдаётся шагом `NuGet/login@v1` и живёт около часа — pack/push должны идти подряд (так и сделано в workflow).

## 4. После публикации

В **CascadeIDE** заменить `ProjectReference` на `externals/avaloniaedit` на `PackageReference` с версией, которую только что запушили (и при необходимости убрать субмодуль).

## Ссылки

- [Trusted Publishing on nuget.org](https://learn.microsoft.com/nuget/nuget-org/trusted-publishing)
- [NuGet/login action](https://github.com/NuGet/login)
