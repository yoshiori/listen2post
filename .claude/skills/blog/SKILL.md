---
name: blog
description: Generate a blog post from a LISTEN podcast episode in Yoshiori's writing style
---

# Podcast Episode → Blog Post

## Core principle

**This is a rewrite task, not a transcription task.**

The transcript is raw spoken audio: filler words, repetition, incomplete sentences, topic jumps.
A blog post is a restructured written artifact. Same content, completely different form.

If your output looks anything like the transcript — same sentence order, same spoken rhythm, same length ratio — you have failed the task.

## Steps

1. Preflight: check `OBSIDIAN_VAULT` env var is set. If not, abort with: "OBSIDIAN_VAULT が未設定です。`mise.toml` を設定して、このディレクトリで `claude` を起動してください。" and stop.
2. Default podcast: `ヨシオリの声日記` (`01hy2gyy79qt8bsbz01grf720e`). Skip podcast selection unless the user explicitly asks for a different one (in which case call `get_my_podcasts` and let them choose).
3. Call `get_podcast_episodes` with the default podcast id and show the episode list. **Remember each episode's `title` and `pubDate` — you need them for the filename.**
4. Ask the user which episode to turn into a blog post
5. Call `get_episode_transcript` with format `txt`
6. **Read the full transcript silently. Do not show it to the user.**
7. Extract the 2–4 real topics discussed. Discard throat-clearing, tangents, and meta-comments about the podcast itself.
8. For each topic, write a `##` section as polished prose. Reorder, merge, and cut freely.
9. Write a blog title that captures the main theme — not "エピソードXXのまとめ". (This `# ` title can differ from the LISTEN episode title; the filename uses the LISTEN title.)
10. Write the finished post to `${OBSIDIAN_VAULT}/Notes/listen2post/<YYYY-MM-DD>-<sanitized LISTEN title>.md`:
    - Date: first 10 chars of the episode's `pubDate`.
    - Sanitize title: replace any of `/ \ : * ? " < > |` and newlines with `-`. Trim whitespace. Keep Japanese characters as-is.
    - If `Notes/listen2post/` doesn't exist, create it first with `mkdir -p` via Bash.
    - Use the `Write` tool to create the file.
11. Output only the absolute file path of the written file. No post body, no preamble, no "書き出しました" message — just the path on one line.

## Hard rules (do not violate)

- ❌ Do NOT paste transcript sentences verbatim into the output
- ❌ Do NOT preserve the spoken order if a different order reads better
- ❌ Do NOT include "今日は〜について話します" or any intro that announces the topic
- ❌ Do NOT end with "まとめ" / "おわりに" sections or "というわけで今日はこんな感じでした" closers
- ❌ Do NOT include timestamps, speaker labels, or "[笑]" style annotations
- ❌ Do NOT show the raw transcript to the user at any point
- ❌ Do NOT echo the post body into the chat after writing the file — only the file path
- ❌ Do NOT stack exclamation parentheticals ("（これホントに）", "（すごい気に入ってる）") — use them sparingly, max once per post
- ❌ Do NOT invent vocabulary the speaker didn't use (e.g. don't expand "UI" to "UI/UX", don't add "UX" / "体験設計" etc. unless the transcript did)
- ❌ Do NOT explain jargon the speaker left unexplained — match the reader assumption of the original (e.g. if Yoshiori says "M5Stack" without defining it, don't add "（ディスプレイ付き Arduino）")
- ✅ DO start in medias res, straight into the substance
- ✅ DO cut ruthlessly — a 20-minute episode often becomes a short post
- ✅ DO preserve "なぜそれを選んだか" details when present (e.g. "3Dプリンタ持ってないのでレゴ"). Constraint-driven choices make the post less generic
- ✅ DO match the speaker's emotional temperature. If they sound calm and matter-of-fact, the post should too — don't amp it up with extra 感嘆

## Style (Yoshiori voice)

- 口語体ベースだが、読み物として整えた文章
- 「ですます調」は基本使わず、「〜んですよね」「〜なんですよ」「〜じゃないですか？」
- 接続詞は「で、」「なので」「まぁ」を多用
- カジュアル語彙：「雑に」「ガッと」「めっちゃ」「とりあえず」
- 自己ツッコミ：「まぁ、それはそう」「そりゃそうか」「知らんけど」
- 括弧で本音補足：「（これホントに褒めてほしい）」「（まぁ結局やるんだけど）」
- 「！！！！」は本当に興奮している箇所だけ。連発しない
- 技術的な話でも感情を出す。淡々と説明しない

## Transformation example

**Transcript fragment (raw):**
> えーっと、今日はですね、あの、最近、なんか、TypeScriptの、型パズルみたいなやつを、まぁちょっとやってたんですけど、で、なんか、conditional typesってあるじゃないですか、あれで、まぁ、なんていうか、ちょっとハマって、でも結局動いたんで、良かったです。

**Rewritten (blog form):**
> ## TypeScriptの型パズルにハマった
>
> 最近 conditional types でガッと型パズルやってて、これが想像以上にハマった。一見シンプルな分岐なのに、ユニオン型が絡むと distributive になって挙動が変わるんですよね。
>
> 結局 `[T] extends [U]` でタプルに包んで分配を止める、みたいなやつで解決。動いたときは「おお〜」ってなった（これホントに気持ちいい）。

Notice: filler removed, content expanded with the actual technical substance, voice stays personal, no intro/outro scaffolding.

## Before outputting, self-check

Ask yourself:
1. Did I paste any sentence directly from the transcript? → Rewrite it.
2. Is there an intro announcing the topic? → Delete it.
3. Is there a conclusion/summary section? → Delete it.
4. Does each `##` section read as standalone prose, not a transcription? → Fix if not.
5. Does it sound like Yoshiori, or like a neutral AI summary? → Inject voice.
6. Am I using more than one emphatic parenthetical ("（これホントに）" etc.)? → Cut to at most one.
7. Did I add any term the speaker didn't say (UX, 体験設計, fancy synonyms)? → Revert to their vocabulary.
8. Did I define jargon the speaker left undefined? → Remove the gloss — trust the reader.
9. Is the emotional temperature louder than the audio? → Tone down. Matter-of-fact stays matter-of-fact.

Only after all pass, output the post.

## Output format

File contents: Hatena Blog Markdown. Title as `# タイトル`. Section headers as `##`. No code fences around the whole thing.

Chat output after writing: a single line containing only the absolute path to the written `.md` file.
