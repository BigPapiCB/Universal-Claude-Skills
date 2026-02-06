# Injection Prevention Patterns

## SQL Injection

```
NEVER: "SELECT * FROM users WHERE id = '" + userInput + "'"
ALWAYS: Parameterized queries
```

```typescript
// Node (pg)
db.query('SELECT * FROM users WHERE id = $1', [userId]);
```

```python
# Python (psycopg2)
cursor.execute('SELECT * FROM users WHERE id = %s', (user_id,))
```

## XSS (Cross-Site Scripting)

```
NEVER: innerHTML = userInput
NEVER: dangerouslySetInnerHTML={{ __html: userInput }}
ALWAYS: Use text content or framework escaping
```

- React JSX auto-escapes by default. Only `dangerouslySetInnerHTML` is dangerous.
- Escape in ALL contexts: HTML attributes, URLs, CSS, JavaScript
- Set `Content-Security-Policy` headers

## Command Injection

```
NEVER: exec(`convert ${filename} output.png`)
ALWAYS: Use arrays for command arguments
```

```python
subprocess.run(['convert', filename, 'output.png'], check=True)
```

```typescript
execFile('convert', [filename, 'output.png']);
```

## Path Traversal

```typescript
const safePath = path.resolve(uploadsDir, userFilename);
if (!safePath.startsWith(uploadsDir)) throw new Error('Invalid path');
```

## CSRF Prevention

- Use anti-CSRF tokens for state-changing requests
- Check `Origin` and `Referer` headers
- Use `SameSite=Strict` or `Lax` on cookies
- Don't use GET for state changes

## Open Redirect

```
NEVER: res.redirect(req.query.returnUrl)
ALWAYS: Validate against allowlist or use relative paths only
```

```typescript
const allowed = ['/dashboard', '/profile', '/settings'];
const target = allowed.includes(returnUrl) ? returnUrl : '/dashboard';
res.redirect(target);
```
