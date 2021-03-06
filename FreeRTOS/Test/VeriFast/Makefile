VERIFAST ?= verifast
VERIFAST_ARGS = -I include -c $(EXTRA_VERIFAST_ARGS)

ifeq ($(NO_COVERAGE), 1)
check_coverage = cat
else
check_coverage = perl -pe \
  'END { \
      if ($$status) { \
        print "Coverage regression failed: Expected $1 statements verified.\n"; \
      } \
      exit $$status; \
   } \
   $$status=/$1 statements verified/ ? 0 : 1;'
endif

all: queue

.PHONY: queue
queue:
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/create.c                    | $(call check_coverage,317)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/prvCopyDataFromQueue.c      | $(call check_coverage,301)
	@$(VERIFAST) $(VERIFAST_ARGS) -disable_overflow_check queue/prvCopyDataToQueue.c        | $(call check_coverage,329)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/prvIsQueueEmpty.c           | $(call check_coverage,282)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/prvIsQueueFull.c            | $(call check_coverage,282)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/prvLockQueue.c              | $(call check_coverage,283)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/prvUnlockQueue.c            | $(call check_coverage,297)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/uxQueueMessagesWaiting.c    | $(call check_coverage,285)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/uxQueueSpacesAvailable.c    | $(call check_coverage,283)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/vQueueDelete.c              | $(call check_coverage,280)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueueGenericSend.c         | $(call check_coverage,328)
	@$(VERIFAST) $(VERIFAST_ARGS) -disable_overflow_check queue/xQueueGenericSendFromISR.c  | $(call check_coverage,310)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueueIsQueueEmptyFromISR.c | $(call check_coverage,280)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueueIsQueueFullFromISR.c  | $(call check_coverage,280)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueuePeek.c                | $(call check_coverage,328)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueuePeekFromISR.c         | $(call check_coverage,293)
	@$(VERIFAST) $(VERIFAST_ARGS)                         queue/xQueueReceive.c             | $(call check_coverage,330)
	@$(VERIFAST) $(VERIFAST_ARGS) -disable_overflow_check queue/xQueueReceiveFromISR.c      | $(call check_coverage,307)

.PHONY: proof_changes
proof_changes:
	@git grep "if[n]*def VERIFAST" | cut  -f 3- -d ' ' | sort | uniq

GIT?=git
NO_CHANGE_CHECKOUT_DIR=no-change-check-freertos-kernel
NO_CHANGE_EXPECTED_HASH=587a83d6476
.PHONY: synced_with_source_check
synced_with_source_check:
	@rm -rf $(NO_CHANGE_CHECKOUT_DIR)
	@$(GIT) clone https://github.com/FreeRTOS/FreeRTOS-Kernel.git $(NO_CHANGE_CHECKOUT_DIR)
	@cd $(NO_CHANGE_CHECKOUT_DIR) && $(GIT) diff --quiet $(NO_CHANGE_EXPECTED_HASH) queue.c
	@cd $(NO_CHANGE_CHECKOUT_DIR) && $(GIT) diff --quiet $(NO_CHANGE_EXPECTED_HASH) include/queue.h
