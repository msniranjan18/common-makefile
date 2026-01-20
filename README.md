# common-makefile
common-makefile

# In your Project Repo
git submodule add https://github.com/msniranjan18/common-makefile.git build/common

# In your Project Makefile
Use the include directive to import the common targets.
```
-include build/common/common.mk
```


# The "Self-Updating" Logic for your Project Makefile
Add this to the top of your Project-specific Makefile (the one that lives in your main repo). This logic ensures that before any common target (like lint or swagger) runs, the submodule is present.

```
# --- Submodule Management ---
COMMON_DIR := build/common

# Target to ensure the submodule is initialized and updated
.PHONY: init-common
init-common:
	@if [ ! -f $(COMMON_DIR)/common.mk ]; then \
		echo "Submodule missing. Initializing..."; \
		git submodule update --init --recursive; \
	fi

# --- Import Common Targets ---
# We use -include so make doesn't crash before init-common runs
-include $(COMMON_DIR)/common.mk

# --- Automatic Trigger ---
# This forces 'init-common' to run before 'swagger', 'lint', etc.
# by making the common targets depend on the init target.
swagger: init-common
lint: init-common
build: init-common
dep: init-common
```
