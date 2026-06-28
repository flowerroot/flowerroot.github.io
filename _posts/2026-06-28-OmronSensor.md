---
layout: single
title: "[C++] Omron 변위센서 데이터파싱."
categories: Cpp
tag: [Cpp]
toc: true
toc_sticky: true
---



## Omron 변위센서 사용기.

### 사용 과정
HONDA & LGES 向 미국 텍사스 SIMWON(명신산업) 2차전지 배터리케이스 측정기 프로젝트가 시작되었다.

본격적으로 미국에서 Setup을 시작하기 전, 경상북도 구미의 TST 공장에서 Demo기 Setup을 먼저 시작했다.

내가 맡은 메인 파트는 Omron사의 변위센서를 활용한 제품의 두께 측정이었다.

ZP-L 시리즈의 제품이었고 구성품은 헤드와 앰프였다.

앰프는 레이저를 이용해 제품과의 거리를 측정하였고 앰프를 두 개 이상 사용하면 각 앰프와 제품간의 거리정보를 활용하여 두께를 측정하는 원리였다.

앰프는 헤드와 연결되었으며 헤드는 IP셋팅, LAN통신 기반 PC연결 등의 역할을 수행한다.

PC는 Command를 사용하여 센싱 데이터를 수신할 수 있었다.



### 문제점
역시 어느 사이트든 일이 쉽게 끝나는 법은 없다.

측정기의 컨셉은 라인스캔 방식이다.

하지만 Omron사 직원은 변위센서가 스캔방식을 지원하지 않는다고 한다.

정지상태에서 단일위치 측정만 가능하다고 한다.

'MA' Command 를 사용하여 현재 센싱값만 단일 취득하는 방식을 지원한다고 했다.

제품의 장변 측정포인트는 8곳 이었기 때문에

스캔하다 여덟 번 멈춰서 측정하거나, 스캔 중 측정 Command를 쉼없이 날려가며 측정포인트를 유추해 사용하는 수 밖에 없다는 소리다.



### 해결 방안
변위센서의 매뉴얼을 정독한 결과 'LB' 라는 Command 가 있었다.

센서의 모든 데이터를 수신하는 명령어다.

해당 명령어를 쏘아보았는데 16진수 기반의 무수히 많은 데이터가 쏟아져 나왔다.

데이터구조를 열심히 파악해본 결과...



### 데이터구조
데이터는 'MA' Command 와 동일하게 16진수 값이 수신됐지만, 양이 훨씬 많았고 의미없는 값이 대부분을 차지했다.

우선 하나의 헤드는 20개의 앰프를 연결할 수 있다. (아마도 그 정도 였던 것 같다)

'LB' Command 를 사용하면 앰프가 1개만 연결되어도 연결되지 않은 19개의 앰프값까지 모두 수신되는 상태다.

20개의 앰프값은 묶여 1개의 블록을 형성한다.

그리고 아주 많은 양의 블록들이 수신된다.

블록 내부 데이터는 블록이 증가 할 때마다 Shift 된다.

첫번째 블록에서는 첫번째 앰프의 값이 첫번째에 위치하지만,

두번째 블록에서는 두번째 앰프의 값이 두번째에 위치하며 동시에 마지막 앰프의 값이 첫번째에 위치하게 된다.

따라서 나는 수신된 모든 데이터를 블록 단위로 구분하고, 블록 Index가 증가할 때 마다 Index 만큼 역 Shift 해줘야 했다.

그리고 마지막엔 16진수를 bit shift 시켜준 후 10진수로 변환해 내가 원하는 앰프의 센싱값을 취득하면 되는 것이었다.



### 데이터 수신 및 파싱 흐름
```cpp
void CInspectAvi::ReceiveAndParseAllLBBlocks(SOCKET sock, std::vector<int32_t>& vecLbDataCh1, std::vector<int32_t>& vecLbDataCh2) {
	// 헤드와 연결된 첫번째 앰프가 블록 내에 위치하는 자리 순서
	const std::unordered_set<int> ch1Indices = { 2, 23, 44, 65, 86, 107, 128, 149, 170, 191, 212, 233 };
	// 두번째 앰프
	const std::unordered_set<int> ch2Indices = { 3, 24, 45, 66, 87, 108, 129, 150, 171, 192, 213, 234 };
	// 세번째 앰프도 연결한다면 4, 25, 46... 순서가 될 것이다.

	// 데이터 수신 및 파싱
	while (true) {
		std::vector<uint8_t> lbData;
		// 데이터 수신
		if (!ReceiveLBData(sock, lbData, 10000, 1)) {
			break;
		}

		// 블록 단위를 구분
		std::vector<LBBlock> blocks = parseLBBlocksFromBytes(lbData);

		for (size_t i = 0; i < blocks.size(); i++) {
			size_t usableSize = blocks[i].payload.size() - (blocks[i].payload.size() % 4);
			// 블록 인덱스만큼 역 Shift
			rotateRightBytes(blocks[i].payload, blocks[i].index);

			for (size_t j = 0; j < usableSize; j += 4) {
				int nIndexJ = static_cast<int>(j / 4);

				// Binary To Integer
				int32_t rawVal = parseInt32LE(&blocks[i].payload[j]);
				if (ch1Indices.count(nIndexJ)) {
					vecLbDataCh1.push_back(rawVal);
				}
				else if (ch2Indices.count(nIndexJ)) {
					vecLbDataCh2.push_back(rawVal);
				}
			}
		}
	}
}
```



### Raw 데이터 전체 수신

```cpp
bool CInspectAvi::ReceiveLBData(SOCKET sock, std::vector<uint8_t>& fullData, int maxOverallMs, int idleTimeoutMs) {
	fullData.clear();
	const int bufferSize = 4096;
	std::vector<uint8_t> buffer(bufferSize);

	auto start = std::chrono::steady_clock::now();
	auto lastDataTime = start;

	while (true) {
		// 시간 경과 측정
		auto now = std::chrono::steady_clock::now();
		int totalElapsed = std::chrono::duration_cast<std::chrono::milliseconds>(now - start).count();
		int idleElapsed = std::chrono::duration_cast<std::chrono::milliseconds>(now - lastDataTime).count();

		if (totalElapsed > maxOverallMs) {
			//std::cerr << "[TIMEOUT] 전체 수신 시간 초과" << std::endl;
			return false;
		}

		// select로 수신 가능 여부 체크
		fd_set readfds;
		FD_ZERO(&readfds);
		FD_SET(sock, &readfds);

		timeval timeout;
		timeout.tv_sec = 0;
		timeout.tv_usec = 100 * 1000;  // 100ms 간격

		int ready = select(0, &readfds, nullptr, nullptr, &timeout);
		if (ready > 0) {
			int len = recv(sock, reinterpret_cast<char*>(buffer.data()), bufferSize, 0);
			if (len > 0) {
				fullData.insert(fullData.end(), buffer.begin(), buffer.begin() + len);
				lastDataTime = std::chrono::steady_clock::now();  // 타이머 리셋

				size_t n = fullData.size();
				if (fullData.size() >= 2 &&
					fullData[fullData.size() - 2] == 0x0D &&
					fullData[fullData.size() - 1] == 0x0A) {
					//std::cout << "[OK] \\r\\n 수신 완료" << std::endl;
					return true;
				}
			}
		}

		if (idleElapsed > idleTimeoutMs) {
			std::cerr << "[WARN] recv 대기 timeout (" << idleElapsed << "ms)" << std::endl;
			return false;
		}
	}
}
```



### 블록 파싱
```cpp
std::vector<LBBlock> CInspectAvi::parseLBBlocksFromBytes(const std::vector<uint8_t>& lbData) {
	std::vector<LBBlock> blocks;

	const std::vector<uint8_t> header = { 'L', 'B', ',', '0', '3', 'F', '4', ',' };
	size_t i = 0;

	while (i + header.size() + 3 < lbData.size()) {
		//헤더 매칭 확인
		if (!std::equal(header.begin(), header.end(), lbData.begin() + i)) {
			++i;
			continue;
		}

		//std::string rawHeader(lbData.begin() + i, lbData.begin() + i + header.size());

		i += header.size();

		//블록 인덱스 (2바이트 리틀 엔디언) + 0x00
		if (i + 2 >= lbData.size()) break;
		uint16_t index = lbData[i] | (lbData[i + 1] << 8);
		i += 3; // index(2) + 0x00(1)

		 //payload 끝 (0D 0A) 탐색
		size_t payloadStart = i;
		size_t payloadEnd = i;
		while (payloadEnd + 1 < lbData.size()) {
			if (lbData[payloadEnd] == 0x0D && lbData[payloadEnd + 1] == 0x0A) {
				break;
			}
			++payloadEnd;
		}

		std::vector<uint8_t> payload(lbData.begin() + payloadStart, lbData.begin() + payloadEnd);
		blocks.push_back(LBBlock{ index, payload });

		//다음 블록 시작점
		i = payloadEnd + 2;
	}

	return blocks;
}
```



### 역 Shift
```cpp
void CInspectAvi::rotateRightBytes(std::vector<uint8_t>& data, size_t shiftBytes) {
	if (data.empty() || shiftBytes == 0) return;

	if (shiftBytes >= data.size()) return;

	// 마지막 바이트 제거
	data.pop_back();

	shiftBytes %= data.size();
	if (shiftBytes == 0) return;

	std::rotate(data.begin(), data.end() - shiftBytes, data.end());
}
```



### Binary To Integer

```cpp
int32_t CInspectAvi::parseInt32LE(const uint8_t* data) {
	return static_cast<int32_t>(data[0]) |
		(static_cast<int32_t>(data[1]) << 8) |
		(static_cast<int32_t>(data[2]) << 16) |
		(static_cast<int32_t>(data[3]) << 24);
}
```



### 파싱 후 데이터 csv 저장
```cpp
void CInspectAvi::SaveOmronData(int nIndex, const LPCTSTR lpszName, const std::vector<int32_t>(&vecOmron)[2][4]) {
	const std::string logDir = CW2A(lpszName);
	const std::string logFile = logDir + "\\[" + std::to_string(nIndex) + "]Omron.csv";
	CVisionSystem::Instance()->createDirectoryIfNotExists(logDir);
	Logger logger(logFile);

	int nSize = (int)vecOmron[nIndex][0].size();
	for (int i = 0; i < 4; i++) { // vector out of range 를 대비한 min size 사용.
		if (nSize > (int)vecOmron[nIndex][i].size())
			nSize = (int)vecOmron[nIndex][i].size();
	}

	for (int i = 0; i < nSize; i++) {
		std::string message;
		message +=
			std::to_string(vecOmron[nIndex][0][i]) + "," +
			std::to_string(vecOmron[nIndex][1][i]) + "," +
			std::to_string(vecOmron[nIndex][2][i]) + "," +
			std::to_string(vecOmron[nIndex][3][i]);
		logger.log(LogLevel::INFO_, message);
	}
}
```



### 그 이후..
csv 에는 스캔 시작지점 부근과 종료지점 부근에 측정된 쓰레기값도 포함되어 있다.

앞 뒤로 비정상적인 값을 잘라낸 후에 정상적인 값만 추리고,

측정대상 위에 값을 나열한다.

측정위치에 해당하는 값을 측정 값으로 사용한다.

정지위치에서 측정할 수 없었기 때문에 이러한 방식이 최선이었다.

다만, 값의 양이 무수히 많았기 때문에 'MA' Command 를 쉴새없이 날리는 것 보단 정확했을 것이다.



약 1년 전 작성했던 코드인데 오랜만에 보니 나도 좀 헷갈린다..ㅋ



끝.