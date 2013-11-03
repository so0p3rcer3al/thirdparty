CXXFLAGS += -g -DNDEBUG -O3 -march=native
ARFLAGS = rvs

all:
	$(CXX) $(CXXFLAGS) -shared -fPIC -ftls-model=initial-exec gnuwrapper.cpp -o libltalloc.so
	if ! $(CXX) $(CXXFLAGS) -flto -c gnuwrapper.cpp -o libltalloc.o; then $(CXX) $(CXXFLAGS) -c gnuwrapper.cpp -o libltalloc.o; fi
	$(AR) $(ARFLAGS) libltalloc.a libltalloc.o
	$(RM) libltalloc.o

clean:
	-$(RM) libltalloc.a libltalloc.so
