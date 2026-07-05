# AGENTS.md

## Project Overview

This repository is a Go study workspace. Keep examples small, readable, and focused on the concept being practiced.

## Working Guidelines

- Prefer clear, idiomatic Go over clever solutions.
- Keep changes scoped to the task at hand.
- Do not rewrite unrelated files or reorganize directories unless requested.
- Preserve any user changes already present in the workspace.

## Go Knowledge Notes

- Only write generated Go learning content to Markdown files when the user explicitly asks to "写入md文档" or uses an equivalent request.
- For normal questions, explanations, or code examples, answer in chat without creating or updating note files.
- Before writing notes, summarize the generated Go content into concise study points.
- Organize notes by Go topic under the `notes/` directory. Use lowercase English filenames such as `notes/struct.md`, `notes/string.md`, `notes/slice.md`, `notes/map.md`, `notes/interface.md`, and `notes/goroutine.md`.
- If the topic file does not exist, create it. If it already exists, append only genuinely new or missing knowledge.
- Before appending, check the target Markdown file for existing content. If the same or highly similar point is already present, do not write it again.
- If the existing note is related but incomplete, add only the missing details instead of repeating the full explanation.
- Each written note should include a title, core concepts, Go example code, and common cautions or gotchas.
- Write note content in Chinese. Use fenced code blocks marked as `go` for Go examples.
- Keep notes easy to review: prefer compact explanations, practical examples, and clear bullet points.

## Common Commands

Run these from the repository root when applicable:

```powershell
go test ./...
go fmt ./...
go vet ./...
```

If the project has no Go module yet, initialize one only when needed:

```powershell
go mod init example.com/golang-study
```

## Code Style

- Format Go code with `gofmt` or `go fmt`.
- Use descriptive names for examples and exercises.
- Keep package structure simple unless the exercise needs otherwise.
- Add comments only when they explain non-obvious behavior.

## Testing

- Add or update tests for behavior changes.
- Prefer table-driven tests for multiple input/output cases.
- Run `go test ./...` before reporting work as complete when tests exist.

## Safety

- Do not run destructive git commands unless explicitly requested.
- Do not delete user-created files without confirmation.
- Avoid adding external dependencies for simple exercises.
