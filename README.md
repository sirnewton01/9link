# 9Link - Proposal

Current hyperlinks (URL's, URI's, URN's, ...) were designed in a world with a plethora of protocols and legacy systems attempting to allow a unified encoding scheme for resources links that can be used in documents. Popular OS filesystems of the day had complex semantics and made numerous assumptions about fast, reliable and direct access to storage devices, which necessitated the light-weight concept of the resource. Provided that the system you are using (e.g. web browser) had the right capabilities, add-ons, network connectivity and system application then the resource can be accessed. There is much complexity in this chain and so much can go wrong.

Web browsers became increasingly the single large application used to navigate all hyperlinks. Over time, many of the old protocols, such as telnet and ftp, fell into disuse and the web browsers stopped supporting them. At the current time the vast majority of links are either http or increasingly https, making the protocol redundant information in every link. Some web browsers now hide the prefix entirely in the address bar unless you click or copy and paste. Most anchor tags attempt to hide the links as much as possible behind a simplified description leading to link length and complexity explosions.

Meanwhile, Plan 9 was developed with a light-weight flexible filesystem protocol called 9P. This protocol removed the core assumptions that files must be on local devices and vastly simplified the file semantics over previous Unix systems. Since the OS makes it easy, fast and safe to mount 9P filesystems an application doesn't need to understand all the different protocols. Filesystems can expose capabilities from different protocols as files so that any application can access them using the simplified semantics. In this environment hyperlinks are assumed to be to other 9P resources, either local or remote. System documents and the Acme editor already take advantage of hyperlinks using relative paths, but they do not yet handle links to remote systems relying instead on the user to mount them manually. There is room for some improved automation and linking that extends beyond the local filesystem and into the internet at large.

9Link is an attempt to redesign hyperlinks given the current context and the lessons learned from existing designs. These new links should be usable in environments with rich applications, such as web browsers, and also environments where there is a unified filesystem, such as Plan 9. The focus of the design is on simplicity, consistency and readability. With better links there should be no need to hide them and then users have all of the information they need to establish their level of trust.

## Design

What information is required in a link in order to successfully access a file over the network? A host name (or IP address) and port number is needed to make a network connection to the remote system. Once you have access to that remote system then some sort of path is needed to address the particular file. Here are some examples.

```
host!port:/my/path  # Explicit host, port and path
host:/my/path       # Explicit host, implied port (443 for https, 564 or 9P)
```

Within the confines of an existing filesystem (or service) links can be relative. Relative links help to make documents easier to copy and host elsewhere. They can also help to make them shorter and easier to read.

```
my/path             # Relative path from the current file
../other/path       # Another relative path to a resource higher in the tree
```

Links can also refer to elements in your own filesystem. Some files, such as configuration files or system files can vary depending on context. 9Links allow you to refer to files that can be found in the local filesystem.

```
/sys/doc            # Absolute path in the filesystem to the system's documentation
```

Sometimes there is a need to identify a particular line in the file, and not just the beginning. In the world of text files there is a well established convention for linking to a particular line within a file. You can find them in compiler error or warning messages. The Plan 9 acme editor detects these kinds of links and allow you to navigate directly to the line number.

```
foo.txt:129
host:/my/path/foo.txt:129
```

In other cases line numbers are not sufficient. The line number could change and the link should be resilient to such changes. In some types of files, such as binaries, the line number may not make sense as an anchor point. To work around this links can refer to a particular unique snippet of text found within the document.

```
some/path/foo.txt:/Introduction/
host:/my/path/foo.txt:/Let there be peace, not war/
```

All 9Links are encoded using UTF-8 making it possible to use a very large variety of characters in the links. The only reserved characters are '!', ':', and '/' so that parsers can easily distinguish the various aspects of the link.

No escaping is necessary in a 9Link. The major markup languages, such as HTML and Markdown already have mechanisms for determining the end of a link. Plan 9's Acme has ways of allowing the user to select the extent of the link that they want to navigate. Spaces are discouraged, but possible as they are for file names.

## Conclusion

The 9Link proposal redefines hyperlinks so that they have much less redundant information. They are easier to read and understand eliminating the need to hide or label them. Also, the same kinds of links can be used in the world wide web and Plan 9 systems in a consistent way. Plan 9 can make use of the 9Links to help and automate the navigation of remote files. With these changes hyperlinks can hopefully become more readable to the users allowing them to better develop their level of trust. There would be no need to hide them behind anchor tags.

## Notes
The concept of URL query was intentionally omitted. They add another layer to the design, which increases complexity. Queries dynamically affect the output of a particular resource. This is both an antipattern in modern REST design and in Plan 9 filesystem design. This kind of information passing is best accomplished using a control file that configures the output of other file(s). URL queries are often misused to hack in hints from the client to the server, such as user credentials and other things. It is better to leave this out of the 9Link design.

Do you have any issues with this proposal? If so, you can raise them here https://github.com/sirnewton01/9link/issues