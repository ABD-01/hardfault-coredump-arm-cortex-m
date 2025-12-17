1. memfault is used as is in the submodule form

2. shell used with following changes

shell/Makefile                              -> Makefile  
shell/build-magic/configuration.mk          -> build-magic/configuration.mk  
shell/build-magic/defaults.mk               -> build-magic/defaults.mk  
shell/build-magic/find-headers.mk           -> build-magic/find-headers.mk  
shell/build-magic/find-source.mk            -> build-magic/find-source.mk  
shell/lib/a2i/a2i.c                         -> lib/a2i/a2i.c  
shell/lib/a2i/a2i.h                         -> lib/a2i/a2i.h  
shell/lib/memlog/memlog.c                   -> lib/memlog/memlog.c  
shell/lib/memlog/memlog.h                   -> lib/memlog/memlog.h  
shell/lib/printf/printf.c                   -> lib/printf/printf.c  
shell/lib/printf/printf.h                   -> lib/printf/printf.h  
shell/lib/regs/regs.h                       -> lib/regs/regs.h  
shell/lib/string/string.c                   -> lib/string/string.c  
shell/lib/string/string.h                   -> lib/string/string.h  
shell/lib/utils/utils.c                     -> lib/utils/utils.c  
shell/scatter/one-seg.ld                    -> scatter/one-seg.ld  
shell/scatter/two-seg.ld                    -> scatter/two-seg.ld  
shell/shell/shell.c                         -> shell/shell.c  
shell/shell/shell.h                         -> shell/shell.h  
shell/shell/shell_config.h                  -> shell/shell_config.h  
shell/startup/aarch64.S                     -> startup/aarch64.S  
shell/startup/arm32.S                       -> startup/arm32.S  
shell/startup/cortex-m.S                    -> startup/cortex-m.S  
shell/startup/riscv64.S                     -> startup/riscv64.S  
examples/physical/tiva-c/platform/start.S   -> startup/riscv32.S  

Rest all files are deleted

Following changes in `shell/shell.c`

```diff
diff --git a/shell/shell/shell.c b/shell/shell.c
similarity index 99%
rename from shell/shell/shell.c
rename to shell/shell.c
index 2f1459c..53ffe9b 100644
--- a/shell/shell/shell.c
+++ b/shell/shell.c
@@ -359,7 +359,7 @@ static int build_info(int argc, char **argv) {
   return 0;
 }

-
+#if 0
 void __attribute__((optimize("O0"), weak)) initial_setup(void) {
   /**
    * Copy the data section from ROM to RAM and
@@ -401,13 +401,14 @@ void __attribute__((optimize("O0"), weak)) initial_setup(void) {
    */
   platform_init();
 }
+#endif

 /**
  * @brief spwans the prompt
  *
  */
 void prompt() {
-  initial_setup();
+  // initial_setup();
   exec_auto_cmds();
   setup();
```

Following changes in `shell/shell_config.h`

```diff
diff --git a/shell/shell/shell_config.h b/shell/shell_config.h
similarity index 98%
rename from shell/shell/shell_config.h
rename to shell/shell_config.h
index d99e210..d677cbe 100644
--- a/shell/shell/shell_config.h
+++ b/shell/shell_config.h
@@ -21,6 +21,8 @@
 extern "C" {
 #endif

+#include "shell_user_config.h"
+
 /**
  * Build information related macros
  */
```

Following changes in `startup/riscv32.S`

```diff
diff --git a/examples/physical/tiva-c/platform/start.S b/startup/riscv32.S
similarity index 70%
rename from examples/physical/tiva-c/platform/start.S
rename to startup/riscv32.S
index 4904a68..f7b4dce 100644
--- a/examples/physical/tiva-c/platform/start.S
+++ b/startup/riscv32.S
@@ -14,10 +14,11 @@
 # limitations under the License.
 #

-.section .text
-.p2align 2
-.globl reset_handler
-.type reset_handler, %function
-reset_handler:
-    bl prompt
-    bl .
+# Execution starts from here
+.section .init
+.globl _start
+_start:
+    la sp, _STACK_TOP_  # Load the SP with end of RAM
+    jal prompt          # Jump to prompt()
+    j .                 # Spin forever
+```
