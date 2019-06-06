# Errors

<aside class="notice">
This error section is stored in a separate file in <code>includes/_errors.md</code>. Slate allows you to optionally separate out your docs into many files...just save them to the <code>includes</code> folder and add them to the top of your <code>index.md</code>'s frontmatter. Files are included in the order listed.
</aside>

The Kittn API uses the following error codes:

| Error Code | Meaning                                                                                                              |
| ---------- | -------------------------------------------------------------------------------------------------------------------- |
| 400        | Bad Request -- Your request is invalid.                                                                              |
| 401        | Unauthorized -- Your API key is wrong.                                                                               |
| 403        | Forbidden -- You don't have permission to the endpoint.                                                              |
| 404        | Not Found -- The endpoint could not be found.                                                                        |
| 406        | Not Acceptable -- You made an invalid or incorrect request.                                                          |
| 500        | Internal Server Error -- An error occurred on the server. Check your server logs for information.                    |
| 503        | Service Unavailable -- We're temporarily offline for maintenance. This can occur during WordPress or plugin updates. |
