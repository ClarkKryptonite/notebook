# Shell Command

## test disk speed

test write `time dd if=/dev/zero bs=1024k of=tstfile count=1024`
test read `dd if=tstfile bs=1024k of=/dev/null count=1024`
