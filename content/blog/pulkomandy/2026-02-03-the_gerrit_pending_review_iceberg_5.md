+++
type = "blog"
title = "The Gerrit code review iceberg, episode 5"
author = "PulkoMandy"
date = "2026-02-03 13:30:00+0100"
tags = []
+++

Hello everyone!

We continue our exploration of old change requests on the Gerrit code review tool that have been
waiting for several years for someone to pick them up and get them merged. There are currently
316 commits awaiting review in the code review tool. This is quite a bit down from the 350 at the
end of last year. It looks like these blog posts are having some positive effect, and also some
of the developers could spend a bit more time on Haiku during their winter break?

This time we'll be looking at more changes from may and june 2021. Let's see what people were
experimenting with back then!

# [Change 3158](https://review.haiku-os.org/c/haiku/+/3158) - Add a Deskbar location menu

## What is this change about?

It introduces a "location" popup menu in DeskBar preferences. It is one of several
experiments to make the ability to move DeskBar around more discoverable. Currently, the
only affordance for that is a tiny dotted pattern on the edge of the DeskBar. It may have worked
fine on 1990s display resolutions, but today it is a bit too small to do its job.

## Why is this change stalled?

The offered solution is a pop-up menu in DeskBar preferences where you can select various positions.
This works, but maybe a widget where you can drag the DeskBar around (similar to the "active corners"
configuration in ScreenSaver preferences, maybe?) would be better.

Other ideas like making the DeskBar resize knob do something on mouse hover (highlight the borders
of DeskBar, highlight itself, change the mouse cursor) could also be explored. The pop-up menu
is sort-of okay, but doesn't look great.

# [Change 2273](https://review.haiku-os.org/c/haiku/+/2273) - StyledEdit window naming for untitled documents

## What is this change about?

Sometimes small details are hard to get right.

This one is about the naming of new windows in StyledEdit. The first one you open is
"Untitled Document 1". The next one is "Untitled Document 2", and so on. As long as StyledEdit
is running, the number never goes down. So if you create a "Untitled Document 1", and save it
with another name, its window gets renamed. But the next window you open will be "Untitled Document 2" anyways.

This change simply searches for the first available number. This is a simple way to solve the problem
and reuse the first number that's available. Other approaches were discussed (for example, if you have
an "Untitled Document 2" but no "Untitled Document 1", should the next window be 1, or 3?)

## Why is this change stalled?

Independently of the discussion on how to pick the next number, this change creates the window name
in a way that is not convenient for localization. It just appends the number at the end, where in
some languages we may need it at the start, for example it could be "1st untitled document", "2nd untitled document", and so on.

The existing code in StyledEdit is already problematic in that regard even before this change, but
there are other problems in the change that make the situation worse and more complicated. It should
be quite simple to adjust it to use a better localizable formatting, and I think it could then be
merged, even if we continue the discussion on the algorithm to pick the "next" number.

# [Change 1439](https://review.haiku-os.org/c/haiku/+/1439) - Disable 'address of packed member' warning for GCC 9 and later

## What is this change about?

Each new GCC version comes with new warnings about non-standard and possibly broken code.

In this specific case, a warning was added in gcc9 when taking the address of a packed member in a structure.
This is not allowed in standard C because the variable in question may not be aligned to the normally required
memory alignment for its type (that's what "packed" structures do).

On the x86 architecture, this is not really a problem. The code may be less efficient (on early CPU
implementations, not even on modern ones), but it would still work. However, some ARM CPUs will
generate an incorrect memory access in this case, and some SPARC ones will generate a fault, that
can be handled in software to convert the misaligned access in smaller, slower aligned accesses.

## Why is this change stalled?

The proposed fix here was to simply disable the warning. Instead, the correct approach is to alter
the code to not use unaligned accesses, and make sure to access such packed members with safe code.

Some changes in that direction were already merged as part of the work on the SPARC port of Haiku.
There is surely more to do, but disabling the warning simply doesn't look like the correct fix at
this point. This change should probably be abandoned.

# [Change 3180](https://review.haiku-os.org/c/haiku/+/3180) - Wacom Intuos S support

## What is this change about?

Haiku implements a specific driver for Wacom tablets. This driver allows to report pen pressure
and various other tablet-specific information to applications. Wonderbrush makes use of this for
digital painting.

Over the years, Wacom has built many devices, which all use a similar protocol but with variants
as the hardware is tweaked and improved.

This change attempts to add one new model of tablet to the driver, but it was never completed.

## Why is this change stalled?

The author lost interest and could not make all the needed changes. Further study of the Linux driver
is needed to understand what changes to the protocol are needed for this new tablet to work.

# [Change 657](https://review.haiku-os.org/c/haiku/+/657) - VESA brightness control

## What is this change about?

This was an attempt to use VBE/FP (VESA BIOS Extensions for Flat Panel) to control the display
brightness in the VESA driver.

## Why is this change stalled?

The VBE/FP specification only exists as a draft, and no hardware we could find actually implements
it. So, this change isn't very useful and could be abandoned. However, there is another change
that depends on it and uses another mechanism, this time part of the more widely implemented
DPMS. That one may still be of some use. So, removing the parts specific to VBE/FP but keeping the
support for the DPMS change may be a good idea.

