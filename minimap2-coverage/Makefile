CFLAGS=		-g -O2 -Wall -Wc++-compat
CPPFLAGS=	-DHAVE_KALLOC
INCLUDES=
OBJS=		kthread.o kalloc.o misc.o bseq.o sketch.o sdust.o index.o chain.o align.o hit.o map.o format.o pe.o esterr.o ksw2_ll_sse.o lqutils.o lqmap.o
PROG=		minimap2-coverage sdust
PROG_EXTRA=	
LIBS=		-lm -lz -lpthread


ifeq ($(arm_neon),)
ifeq ($(sse2only),)
	OBJS+=ksw2_extz2_sse41.o ksw2_extd2_sse41.o ksw2_exts2_sse41.o ksw2_extz2_sse2.o ksw2_extd2_sse2.o ksw2_exts2_sse2.o ksw2_dispatch.o
else
	OBJS+=ksw2_extz2_sse.o ksw2_extd2_sse.o ksw2_exts2_sse.o
endif
else
	OBJS+=ksw2_extz2_neon.o ksw2_extd2_neon.o ksw2_exts2_neon.o
	INCLUDES+=-Isse2neon
ifeq ($(aarch64),)
	CFLAGS+=-D_FILE_OFFSET_BITS=64 -fsigned-char
else
	CFLAGS+=-D_FILE_OFFSET_BITS=64 -fsigned-char
endif
endif

ifeq  ($(shell uname),Darwin)
    LIBS += -largp
endif

.PHONY:all extra clean depend
.SUFFIXES:.c .o

.c.o:
		$(CC) -c $(CFLAGS) $(CPPFLAGS) $(INCLUDES) $< -o $@

all:$(PROG)

extra:all $(PROG_EXTRA)

minimap2-coverage:minimap2-coverage.o libminimap2.a
		$(CC) $(CFLAGS) $< -o $@ -L. -lminimap2 $(LIBS)

libminimap2.a:$(OBJS)
		$(AR) -csru $@ $(OBJS)

sdust:sdust.c getopt.o kalloc.o lqutils.o kalloc.h kdq.h kvec.h kseq.h sdust.h lqutils.h
		$(CC) -D_SDUST_MAIN $(CFLAGS) $< getopt.o kalloc.o lqutils.o -o $@ -L. $(LIBS)

# SSE-specific targets on x86/x86_64

ksw2_extz2_sse41.o:ksw2_extz2_sse.c ksw2.h kalloc.h
		$(CC) -c -msse4 $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH $(INCLUDES) $< -o $@

ksw2_extz2_sse2.o:ksw2_extz2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH -DKSW_SSE2_ONLY $(INCLUDES) $< -o $@

ksw2_extd2_sse41.o:ksw2_extd2_sse.c ksw2.h kalloc.h
		$(CC) -c -msse4 $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH $(INCLUDES) $< -o $@

ksw2_extd2_sse2.o:ksw2_extd2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH -DKSW_SSE2_ONLY $(INCLUDES) $< -o $@

ksw2_exts2_sse41.o:ksw2_exts2_sse.c ksw2.h kalloc.h
		$(CC) -c -msse4 $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH $(INCLUDES) $< -o $@

ksw2_exts2_sse2.o:ksw2_exts2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH -DKSW_SSE2_ONLY $(INCLUDES) $< -o $@

ksw2_dispatch.o:ksw2_dispatch.c ksw2.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_CPU_DISPATCH $(INCLUDES) $< -o $@

# NEON-specific targets on ARM

ksw2_extz2_neon.o:ksw2_extz2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_SSE2_ONLY -D__SSE2__ $(INCLUDES) $< -o $@

ksw2_extd2_neon.o:ksw2_extd2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_SSE2_ONLY -D__SSE2__ $(INCLUDES) $< -o $@

ksw2_exts2_neon.o:ksw2_exts2_sse.c ksw2.h kalloc.h
		$(CC) -c $(CFLAGS) $(CPPFLAGS) -DKSW_SSE2_ONLY -D__SSE2__ $(INCLUDES) $< -o $@

# other non-file targets

clean:
		rm -fr gmon.out *.o a.out $(PROG) $(PROG_EXTRA) *~ *.a *.dSYM build dist mappy.so mappy.c python/mappy.c mappy.egg*

depend:
		(LC_ALL=C; export LC_ALL; makedepend -Y -- $(CFLAGS) $(CPPFLAGS) -- *.c)

# DO NOT DELETE

align.o: minimap.h mmpriv.h bseq.h ksw2.h kalloc.h
bseq.o: bseq.h kvec.h kalloc.h kseq.h
chain.o: minimap.h mmpriv.h bseq.h kalloc.h
esterr.o: mmpriv.h minimap.h bseq.h
format.o: kalloc.h mmpriv.h minimap.h bseq.h
getopt.o: getopt.h
hit.o: mmpriv.h minimap.h bseq.h kalloc.h khash.h
index.o: kthread.h bseq.h minimap.h mmpriv.h kvec.h kalloc.h khash.h
kalloc.o: kalloc.h
ksw2_extd2_sse.o: ksw2.h kalloc.h
ksw2_exts2_sse.o: ksw2.h kalloc.h
ksw2_extz2_sse.o: ksw2.h kalloc.h
ksw2_ll_sse.o: ksw2.h kalloc.h
main.o: bseq.h minimap.h mmpriv.h getopt.h
map.o: kthread.h kvec.h kalloc.h sdust.h mmpriv.h minimap.h bseq.h khash.h
misc.o: minimap.h ksort.h
pe.o: mmpriv.h minimap.h bseq.h kvec.h kalloc.h ksort.h
sdust.o: kalloc.h kdq.h kvec.h sdust.h
sketch.o: kvec.h kalloc.h minimap.h

# YF added.
minimap2-coverage.o: minimap.h kseq.h minimap2-coverage.h
lqmap.o: kthread.h kvec.h kalloc.h sdust.h mmpriv.h minimap2-coverage.h bseq.h khash.h minimap2-coverage.h
lqutils.o: lqutils.h minimap2-coverage.h
