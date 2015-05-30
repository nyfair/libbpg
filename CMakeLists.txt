PROJECT(libbpg)

CMAKE_MINIMUM_REQUIRED(VERSION 2.8.10)

SET(CONFIG_BPG_VERSION "0.9.5")

SET(SOURCE_FILES
	jctvc/TAppEncCfg.cpp
	jctvc/TAppEncTop.cpp
	jctvc/program_options_lite.cpp
	jctvc/TLibEncoder/SyntaxElementWriter.cpp
	jctvc/TLibEncoder/TEncSbac.cpp
	jctvc/TLibEncoder/TEncBinCoderCABACCounter.cpp
	jctvc/TLibEncoder/TEncGOP.cpp
	jctvc/TLibEncoder/TEncSampleAdaptiveOffset.cpp
	jctvc/TLibEncoder/TEncBinCoderCABAC.cpp
	jctvc/TLibEncoder/TEncAnalyze.cpp
	jctvc/TLibEncoder/TEncEntropy.cpp
	jctvc/TLibEncoder/TEncTop.cpp
	jctvc/TLibEncoder/SEIwrite.cpp
	jctvc/TLibEncoder/TEncPic.cpp
	jctvc/TLibEncoder/TEncRateCtrl.cpp
	jctvc/TLibEncoder/WeightPredAnalysis.cpp
	jctvc/TLibEncoder/TEncSlice.cpp
	jctvc/TLibEncoder/TEncCu.cpp
	jctvc/TLibEncoder/NALwrite.cpp
	jctvc/TLibEncoder/TEncCavlc.cpp
	jctvc/TLibEncoder/TEncSearch.cpp
	jctvc/TLibEncoder/TEncPreanalyzer.cpp
	jctvc/TLibVideoIO/TVideoIOYuv.cpp
	jctvc/TLibCommon/TComWeightPrediction.cpp
	jctvc/TLibCommon/TComLoopFilter.cpp
	jctvc/TLibCommon/TComBitStream.cpp
	jctvc/TLibCommon/TComMotionInfo.cpp
	jctvc/TLibCommon/TComSlice.cpp
	jctvc/TLibCommon/ContextModel3DBuffer.cpp
	jctvc/TLibCommon/TComPic.cpp
	jctvc/TLibCommon/TComRdCostWeightPrediction.cpp
	jctvc/TLibCommon/TComTU.cpp
	jctvc/TLibCommon/TComPicSym.cpp
	jctvc/TLibCommon/TComPicYuv.cpp
	jctvc/TLibCommon/TComYuv.cpp
	jctvc/TLibCommon/TComTrQuant.cpp
	jctvc/TLibCommon/TComInterpolationFilter.cpp
	jctvc/TLibCommon/ContextModel.cpp
	jctvc/TLibCommon/TComSampleAdaptiveOffset.cpp
	jctvc/TLibCommon/SEI.cpp
	jctvc/TLibCommon/TComPrediction.cpp
	jctvc/TLibCommon/TComDataCU.cpp
	jctvc/TLibCommon/TComChromaFormat.cpp
	jctvc/TLibCommon/Debug.cpp
	jctvc/TLibCommon/TComRom.cpp
	jctvc/TLibCommon/TComPicYuvMD5.cpp
	jctvc/TLibCommon/TComRdCost.cpp
	jctvc/TLibCommon/TComPattern.cpp
	jctvc/TLibCommon/TComCABACTables.cpp
	jctvc/libmd5/libmd5.c
	libbpg.c
	libavcodec/hevc_cabac.c
	libavcodec/hevc_filter.c
	libavcodec/hevc.c
	libavcodec/hevcpred.c
	libavcodec/hevc_refs.c
	libavcodec/hevcdsp.c
	libavcodec/hevc_mvs.c
	libavcodec/hevc_ps.c
	libavcodec/hevc_sei.c
	libavcodec/utils.c
	libavcodec/cabac.c
	libavcodec/golomb.c
	libavcodec/videodsp.c
	libavutil/mem.c
	libavutil/buffer.c
	libavutil/log2_tab.c
	libavutil/frame.c
	libavutil/pixdesc.c
	libavutil/md5.c
)

IF(MSVC)
	SET(SOURCE_FILES ${SOURCE_FILES} getopt/getopt.cpp)
	INCLUDE_DIRECTORIES(getopt)
ELSE()
	ADD_DEFINITIONS(-D__ANSI__)
ENDIF()

INCLUDE_DIRECTORIES(. jctvc)

ADD_DEFINITIONS(-D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_REENTRANT -DHAVE_AV_CONFIG_H -DUSE_VAR_BIT_DEPTH -DUSE_PRED -DCONFIG_BPG_VERSION=${CONFIG_BPG_VERSION})

SOURCE_GROUP("Source Files" FILES ${SOURCE_FILES} jctvc_glue.cpp x265_glue.c bpgenc.cpp bpgdec.c bpgview.c)

ADD_LIBRARY(libbpg SHARED ${SOURCE_FILES})

FIND_PACKAGE(JPEG)
FIND_PACKAGE(PNG)
IF(JPEG_FOUND AND PNG_FOUND)
	OPTION(ENCODER "Build Encoder" ON)
	OPTION(DECODER "Build Decoder" ON)
	INCLUDE_DIRECTORIES(${JPEG_INCLUDE_DIR} ${PNG_INCLUDE_DIR})
	IF(ENCODER)
		ADD_EXECUTABLE(bpgenc bpgdec.cpp)
		ADD_DEPENDENCIES(bpgenc libbpg)
		TARGET_LINK_LIBRARIES(bpgenc libbpg ${JPEG_LIBRARIES} ${PNG_LIBRARIES})
	ENDIF()
	IF(DECODER)
		ADD_EXECUTABLE(bpgdec bpgdec.c)
		ADD_DEPENDENCIES(bpgdec libbpg)
		TARGET_LINK_LIBRARIES(bpgdec libbpg ${JPEG_LIBRARIES} ${PNG_LIBRARIES})
	ENDIF()
ENDIF()

FIND_PACKAGE(SDL)
IF(SDL_FOUND)
	OPTION(VIEWER "Build Viewer" ON)
	INCLUDE_DIRECTORIES(${SDL_INCLUDE_DIR})
	IF(VIEWER)
		ADD_EXECUTABLE(bpgview bpgview.c)
		ADD_DEPENDENCIES(bpgview libbpg)
		TARGET_LINK_LIBRARIES(bpgview libbpg ${SDL_LIBRARIES})
	ENDIF()
ENDIF()
